---
title: "PVE CT 初体验"
date: 2023-10-07T16:05:43+08:00
draft: false
slug: "Pve-CT"
---

PVE CT是虚拟机的轻量级替代品，PVE 使用[LXC](https://linuxcontainers.org/lxc/introduction/)作为底层容器技术并简化了LXC的使用和管理。

## CT Template换源

docker运行需要image，CT创建则需要template，PVE的Template默认分成了3个section，主要是system和turnkeylinux这2个section，前者提供操作系统的template，有各种发行版的template。后者提供各类应用程序的template，有各种开发者常用软件的template。

CT的system template可以直接从CT源里下载，如果直接使用默认源，下载非常慢，需要将Template的下载地址修改为PVE国内镜像源地址（template源和PVE的apt源是一起的）。turnkeylinux同样有国内源（如ustc），如果有需要同样可以尝试替换它。

template换源参考这篇[博文](https://foxi.buduanwang.vip/virtualization/pve/1071.html/)，换源即修改`/usr/share/perl5/PVE/APLInfo.pm`，修改前注意备份，将里面的`http://download.proxmox.com/images`修改为`https://mirrors.ustc.edu.cn/proxmox/iamge`（以ustc镜像源为例），不过好像PVE系统升级会将它还原，可能升级后需要再次换源。源生效还需要重启pvedaemon（`systemctl restart pvedaemon`）,在web面板的shell重启pvedaemon会造成面板连接断开。

template换源以后就可以直接在web面板里面选择template下载了，只有下载到本地的template才可以在创建CT时选择，不能像docker那样，创建容器时拉取image。

## CT创建和使用

CT创建过程和创建VM过程差不多，但是可选项比较少，创建时可以直接配置ssh密钥和root密码，不需要像VM一样利用cloudinit。启动后CT使用和VM一样，可以通过console直接控制和ssh远程连接。

启动后如果点击console只显示光标，没有命令行提示符，修改Options中的Console mode为shell。

## 问题

### CT无法启动
在第一次启动CT时，我遇到了无法启动问题，报错信息为`WARN: old systemd (< v232) detected, container won't run in a pure cgroupv2 environment! Please see documentation -> container -> cgroup version.
TASK WARNINGS: 1`，翻阅PVE官方文档未找到想要答案，只注意到这个错误应该与systemd有关，但我的主机systemd版本为247，不应该版本过低。

最后运行`apt list --upgradable`时发现systemd可以从`247.3-7+deb11u4`升级到`247.3-7+1-pmx11u1`，后者来源于promox源，前者来源与debian源，意识到可能是debian源的systemd不支持CT所需要的特性，于是`apt upgrade` and `reboot`，重启后CT启动成功。

### PVE更新系统
`apt updgrade`过后再次`apt upgrade` ，发现有3个包显示`kept backed`，虽然可以正常使用，但逼死强迫症。通过[PVE论坛帖子](https://forum.proxmox.com/threads/no-subscription-the-following-packages-have-been-kept-back-pve-kernel-5-15.114272/)发现，我的更新方式有问题。

一般debian的服务器为了稳定都选择使用`apt updgrade`来更新系统，但是PVE需要使用`apt dist-upgrade`，`apt updgrade`会破坏PVE依赖。PVE的web面板里面的更新按钮就是使用`apt dist-upgrade`，PVE[官方手册](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#_system_software_updates)也推荐它。

## 结语

这次折腾CT，主要是因为我想要部署cloudflared来做内网穿透，但是为了这一个程序开一台虚拟机，有点浪费资源，加之一直想要折腾PVE的CT。

CT的使用体验也没有让人失望。CT和host共用内核，不需要像VM一样承担系统固定的资源消耗。我目前使用的CT只运行了一个cloudflared，它日常只使用42mb内存和727mb存储。如果将CT和docker对比，他们最显著的差异就是CT更像一个系统，里面默认可以使用systemd，可以像VM一样通过ssh访问，而且可以像VM一样在PVE web面板控制和备份。虽然docker也可以用systemd和ssh，但需要一些额外操作。

博主认为如果在PVE下如果只是想要运行一些简单程序，可以考虑使用CT，使用它确实可以节省不少内存和存储，而且CT创建也更加简单快速。