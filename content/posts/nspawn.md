---
title: "使用systemd-nspawn搭建开发环境"
date: 2024-05-02T11:08:26+08:00
draft: false
slug: systemd-nspawn-dev-environment
---

过去一个学期，我一直使用systemd-nspawn来搭建各类开发环境。本文记录在Archlinux下使用systemd-nspawn来搭建rcore的开发环境。

# 创建Ubuntu 20.04环境

使用debootstrap创建一个ubuntu环境，详见[Archlinux Wiki](https://wiki.archlinux.org/title/Systemd-nspawn#Create_a_Debian_or_Ubuntu_environment)

首先安装debootstrap和ubuntu-keyring，再使用debootstrap创建ubuntu环境，其中\<container-name\>为我的容器名称。

~~~
# cd /var/lib/machines

# debootstrap --include=dbus,systemd-container --components=main,restricted,universe,multiverse focal <container-name> repository-url
~~~

创建root密码
~~~
# systemd-nspawn -D /var/lib/machines/<container-name>
passwd
logout
~~~

# 网络配置
通过veth来连接host和nspawn，通过nat给nspawn提供外网访问。

nspawn配置文件如下：
~~~
[Network]
VirtualEthernet=yes
~~~

iptables配置：
~~~
# iptables -t nat -A POSTROUTING -s 192.168.163.192/28 -j MASQUERADE
# iptables -A FORWARD -i ve-+ -j ACCEPT
~~~
上面命令只对本次启动起作用，重启后失效。

如果使用ufw，可以选择修改/etc/ufw/before.rules，以实现防火墙规则的持久化。

1. 在before.rules第一个commit前插入：
    ~~~
    -A ufw-before-forward -i ve-+ -j ACCEPT
    ~~~ 

2. 在before.rules文件最底下加入：
    ~~~
    # allow IP masquerading from 192.168.163.192/28
    *nat
    :POSTROUTING ACCEPT [0:0]

    -A POSTROUTING -s 192.168.163.192/28 -j MASQUERADE
    COMMIT
    ~~~

## host侧配置网络地址

编辑容器的service配置文件
~~~
# systemctl edit systemd-nspawn@<container-name>.service
~~~

添加下面3行配置，在容器启动后手动给host端添加ip地址
~~~
[Service]
ExecStartPost=/usr/bin/ip address add 192.168.163.193/28 dev ve-rcore
ExecStartPost=/usr/bin/ip link set dev ve-rcore up
~~~

启用linux的ipv4转发
~~~
# sysctl net.ipv4.ip_forward=1
~~~
如需持久化，需修改文件。ufw用户可选择修改/etc/ufw/sysctl.conf。

## nspawn测网络配置

直接使用systemd-networkd

启用systemd-networkd
~~~
# systemctl enable --now systemd-networkd
~~~

网络配置文件如下：
~~~
[Match]
Name=host0

[Network]
Address=192.168.163.194/28
Gateway=192.168.163.193
DNS=192.168.1.1
~~~

因为我是台式机，网络环境不会变，所以目前dns是固定配置为192.168.1.1。

如果是经常移动的笔记本，可以配置为公共dns地址。

# 其他配置
如果出现terminal type问题和终端颜色异常，尝试在nspawn配置文件中配置一下容器的环境变量
~~~
[Exec]
Environment=TERM=xterm-256color
~~~

# Tailscale ssh

因为我计划使用tailscale ssh来进行远程开发，所以在容器里面部署一下tailscale ssh。

1. 安装tailscale，见[tailscale docs](https://tailscale.com/kb/1039/install-ubuntu-2004)
2. 启用tailscale的用户网络模式，容器环境下不使用tun。修改/etc/default/tailscaled，在FLAGS处加入“--tun=userspace-networking”
3. 进行正常设备认证连接到自己的tailnet
4. 修改tailscale的acl，添加accpet规则，默认check规则ssh连接时需要定期进行网页认证。
    
    添加一个容器的专属tag
    ~~~
    "tagOwners": {
		"tag:containers": ["autogroup:admin"],
	}
    ~~~
    添加ssh的acl规则
    ~~~
    {
		"action": "accept",
		"src":    ["autogroup:member"],
		"dst":    ["tag:containers"],
		"users":  ["autogroup:nonroot", "root"],
	}
    ~~~