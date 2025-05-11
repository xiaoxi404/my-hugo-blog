---
title: "使用 sing-box 配置透明代理"
date: 2025-05-11T00:04:08+08:00
draft: false
slug: singbox-transparent-proxy
---

今年校园网开始限速，对于超过额度的用户会限制访问一些娱乐网站。实验室正好有不限速的网络，我萌生了通过 tailscale 连接实验室跳板香橙派访问受限网站的想法。

sing-box 1.12 更新了对 tailscale 支持了，我也正好借这个周末给宿舍整上了旁路由和透明代理。

# 实验室香橙派配置
参考 [tailscale Exit node](https://tailscale.com/kb/1103/exit-nodes) 文档配置实验室香橙派上的 tailscale 为 exit node。

# 宿舍透明代理配置

我选择了最省事的 tproxy 透明代理，用一个小主机作为旁路由。

## 旁路由配置
网上的 tproxy 透明代理配置非常多，不过都大同小异，我主要参考 Project X 的 [tproxy 透明代理文档](https://xtls.github.io/document/level-2/tproxy.html#netfilter-%E9%85%8D%E7%BD%AE)，不过我做了简化，我不需要代理旁路由自身流量，只需要代理宿舍局域网中其他设备流量。

### 旁路由网络配置
旁路由配置静态 ip，以及 tproxy 所需路由表。

我直接使用 debian 默认网络管理工具 ifupdown 配置网络。

debian 的 ifupdown 网络配置文件位于`/etc/network/interfaces`，我添加了对于旁路由唯一网口 `enp1s0` 的静态 ip 配置，最下面的 `up` 和 `down` hook 是为了网络起来时自动配置 tproxy 所需路由表
~~~
auto enp1s0
allow-hotplug enp1so
iface enp1s0 inet static
	address 192.168.1.245
	netmask 255.255.255.0	
	gateway 192.168.1.1

	up ip rule add fwmark 1 lookup 100
	up ip route add local 0.0.0.0/0 dev lo table 100

	down ip rule del fwmark 1 lookup 100
	down ip route del local 0.0.0.0/0 dev lo table 100
~~~

### 旁路由 nftables 配置
在 `/etc/nftables.conf` 添加如下 table。
~~~
# 创建一个 inet family 的 table
table ip tproxy_redirect {

    # 创建一个 prerouting chain
    chain prerouting {
        type filter hook prerouting priority mangle; policy accept;

        ip daddr 127.0.0.1 return
        ip daddr 10.0.0.0/8 return
        ip daddr 172.16.0.0/12 return
        ip daddr 192.168.0.0/16 return

        ## 劫持所有 dns 流量
        ip saddr 192.168.1.0/24 udp dport 53 tproxy to 127.0.0.1:7891 meta mark set 1
        ip saddr 192.168.1.0/24 tcp dport 53 tproxy to 127.0.0.1:7891 meta mark set 1

        ## 代理所有目的地址为 fakeip 的 tcp 与 udp 流量
        ip saddr 192.168.1.0/24 ip daddr 198.18.0.0/15 ip protocol {tcp, udp} tproxy to 127.0.0.1:7891 meta mark set 1
    }
}
~~~

### 旁路由 sing-box 配置
sing-box 作为整个宿舍的 dns 服务器，对于受限网站返回 fakeip 解析结果，正常网站返回正常 dns 解析结果。route 规则会将受限网站流量都路由到实验室 taiscale exit node 去。 
~~~
{
    "log": {
        "level": "debug"
    },
    "dns": {
        "servers": [
            {
                "tag": "local",
                "type": "h3",
                "server": "223.6.6.6",
                "tls": {
                    "enabled": true
                }
            },
            {
                "type": "fakeip",
                "tag": "fake-ip",
                "inet4_range": "198.18.0.0/15",
                "inet6_range": "fc00::/18"
            },
            {
                "tag": "edu",
                "type": "udp",
                "server": "${学校 dns}"
            }
        ],
        "rules": [
            {
                "domain_suffix": ".edu.cn",
                "action": "route",
                "server": "edu"
            },
            {
                "query_type": [
                    "A",
                    "AAAA"
                ],
                "rule_set": [
                    "category-games",
                    "huya",
                    "douyu",
                    "douyin"
                ],
                "action": "route",
                "server": "fake-ip"
            }
        ],
        "final": "local"
    },
    "experimental": {
        "cache_file": {
            "enabled": true,
            "store_fakeip": true
        }
    },
    "inbounds": [
        {
            "type": "direct",
            "tag": "direct-in",
            "listen": "0.0.0.0",
            "listen_port": 53
        },
        {
            "type": "tproxy",
            "tag": "tproxy-in",
            "listen": "127.0.0.1",
            "listen_port": 7891
        }
    ],
    "endpoints": [
        {
            "type": "tailscale",
            "tag": "ts-ep",
            "exit_node": "${实验室 tailscale 节点名}",
            "domain_resolver": {
                "server": "local"
            }
        }
    ],
    "outbounds": [
        {
            "type": "direct",
            "tag": "direct-out"
        }
    ],
    "route": {
        "rule_set": [
            {
                "tag": "category-games",
            },
            {
                "tag": "huya",
            },
            {
                "tag": "douyu",
            },
            {
                "tag": "douyin",
            }
        ],
        "rules": [
            {
                "action": "sniff",
                "sniffer": "dns"
            },
            {
                "protocol": "dns",
                "action": "hijack-dns"
            },
            {
                "rule_set": [
                    "huya",
                    "douyu",
                    "douyin",
                    "category-games"
                ],
                "action": "route",
                "outbound": "ts-ep"
            }
        ],
        "auto_detect_interface": true,
        "final": "direct-out"
    }
}
~~~
## 路由设置

修改宿舍的小米路由器配置，修改其 dhcp 配置的 dns 服务器和网关为旁路由 ip。

顺便吐槽小米路由器居然不支持配置静态路由:vomiting_face:

# 待优化点
- redirect 透明代理 tcp 流量的性能比 tproxy 高，应该使用 redirect 来代理 tcp 流量，不过宿舍这 100m 小水管应该感受不到性能差异。
- 目前受限网站域名是在宿舍旁路由上解析，最理想情况下应该在实验室网络下解析受限域名。