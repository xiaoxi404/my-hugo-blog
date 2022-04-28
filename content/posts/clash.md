---
title: "Clash 自用配置介绍"
date: 2022-04-28T10:58:04+08:00
draft: false
slug: Clash
---

过去一个月里，一直在不断修改自己的clash配置文件，以求达到最舒服的体验，主要有TUN mode 和 dns这2方面的修改。

## TUN mode

对于一般用户来说使用 ClashForWindows 等工具即可，他们可以帮我们设置系统代理，但是我觉得对于程序员来说，很多时候一些操作需要在终端使用，他们无法走代理，还有uwp应用的沙盒机制，导致他们也无法走代理。而clash Premium的TUN模式就可以完美解决这个问题。

TUN模式下clash虚拟出一张网卡，作为网关，对经过这张网卡的流量进行分流，然后对匹配流量代理。

应用甚至不会感觉到自己走了代理，可以极大的提高我们上网体验。

想要了解他的原理可以看这篇[博文](https://blog.muted.top/2021/11/06/%E5%85%B3%E4%BA%8E%E4%BD%BF%E7%94%A8Clash%E7%9A%84TUN%E9%80%8F%E6%98%8E%E4%BB%A3%E7%90%86%E9%9C%80%E8%A6%81%E7%9F%A5%E9%81%93%E7%9A%84%E4%B8%80%E5%88%87/)

## DNS

clash的dns主要有2个模式，fake-ip 和 redir-host。

fake-ip理论上会获得更好的体验，也是clash的推荐方式，如果想了解可以看clash的[wiki](https://github.com/Dreamacro/clash/wiki/configuration#fake-ip)

但我不是太喜欢fake-ip模式，我选择了使用redir-host模式，他利用2组dns，通过dns-fallback-filter的配置来决定用那一组的结果，我的配置是对于国内ip clash 返回 dns.nameserver 组dns查询的结果，对于国外ip返回 dns.fallback组dns（这里一定要填无污染而且支持DOT or DOH的dns）查询的结果，这能达到极好的dns抗污染效果。

## 自用配置

~~~
# Port of HTTP(S) proxy server on the local end
port: 7890

# Port of SOCKS5 proxy server on the local end
socks-port: 7891

# Transparent proxy server port for Linux and macOS (Redirect TCP and TProxy UDP)
# redir-port: 7892

# Transparent proxy server port for Linux (TProxy TCP and TProxy UDP)
# tproxy-port: 7893

# HTTP(S) and SOCKS4(A)/SOCKS5 server on the same port
# mixed-port: 7890

# authentication of local SOCKS5/HTTP(S) server
# authentication:
#  - "user1:pass1"
#  - "user2:pass2"

# Set to true to allow connections to the local-end server from
# other LAN IP addresses
allow-lan: false

# This is only applicable when `allow-lan` is `true`
# '*': bind all IP addresses
# 192.168.122.11: bind a single IPv4 address
# "[aaaa::a8aa:ff:fe09:57d8]": bind a single IPv6 address
bind-address: '*'

# Clash router working mode
# rule: rule-based packet routing
# global: all packets will be forwarded to a single endpoint
# direct: directly forward the packets to the Internet
mode: rule

# Clash by default prints logs to STDOUT
# info / warning / error / debug / silent
log-level: warning

# When set to false, resolver won't translate hostnames to IPv6 addresses
ipv6: false

# RESTful web API listening address
external-controller: 127.0.0.1:9090

# A relative path to the configuration directory or an absolute path to a
# directory in which you put some static web resource. Clash core will then
# serve it at `http://{{external-controller}}/ui`.
external-ui: path\to\dash-board\directory

# Secret for the RESTful API (optional)
# Authenticate by spedifying HTTP header `Authorization: Bearer ${secret}`
# ALWAYS set a secret if RESTful API is listening on 0.0.0.0
# secret: ""

# Outbound interface name

tun:
  enable: true
  stack: system # or gvisor
  dns-hijack:
    - any:53
    - tcp://any:53
  auto-route: true # auto set global route for Windows
  # It is recommended to use `interface-name`
  auto-detect-interface: true # auto detect interface, conflict with `interface-name`

# fwmark on Linux only
# routing-mark: 6666

# Static hosts for DNS server and connection establishment (like /etc/hosts)
#
# Wildcard hostnames are supported (e.g. *.clash.dev, *.foo.*.example.com)
# Non-wildcard domain names have a higher priority than wildcard domain names
# e.g. foo.example.com > *.example.com > .example.com
# P.S. +.foo.com equals to .foo.com and foo.com
hosts:
  # '*.clash.dev': 127.0.0.1
  # '.dev': 127.0.0.1
  # 'alpha.clash.dev': '::1'

profile:
  # Store the `select` results in $HOME/.config/clash/.cache
  # set false If you don't want this behavior
  # when two different configurations have groups with the same name, the selected values are shared
  store-selected: true

  # persistence fakeip
  store-fake-ip: true

# DNS server settings
# This section is optional. When not present, the DNS server will be disabled.
dns:
  enable: true
  listen: 0.0.0.0:53
  ipv6: false # when the false, response to AAAA questions will be empty

  # These nameservers are used to resolve the DNS nameserver hostnames below.
  # Specify IP addresses only
  default-nameserver:
    - 223.5.5.5
    - 114.114.114.114
  enhanced-mode: redir-host # or fake-ip
  # fake-ip-range: 198.18.0.1/16 # Fake IP addresses pool CIDR
  # use-hosts: true # lookup hosts and return IP record
  
  # Hostnames in this list will not be resolved with fake IPs
  # i.e. questions to these domain names will always be answered with their
  # real IP addresses
  # fake-ip-filter:
  #   - '*.lan'
  #   - localhost.ptlogin2.qq.com
  
  # Supports UDP, TCP, DoT, DoH. You can specify the port to connect to.
  # All DNS questions are sent directly to the nameserver, without proxies
  # involved. Clash answers the DNS question with the first result gathered.
  nameserver:
    #- https://223.5.5.5/dns-query # DNS over HTTPS
    - tls://1.12.12.12:853
    - tls://dns.alidns.com:853
    #- https://223.6.6.6/dns-query # DNS over HTTPS

  # When `fallback` is present, the DNS server will send concurrent requests
  # to the servers in this section along with servers in `nameservers`.
  # The answers from fallback servers are used when the GEOIP country
  # is not `CN`.
  fallback:
     - tls://1.1.1.1:853
     - tls://8.8.8.8:853
  # If IP addresses resolved with servers in `nameservers` are in the specified
  # subnets below, they are considered invalid and results from `fallback`
  # servers are used instead.
  #
  # IP address resolved with servers in `nameserver` is used when
  # `fallback-filter.geoip` is true and when GEOIP of the IP address is `CN`.
  #
  # If `fallback-filter.geoip` is false, results from `nameserver` nameservers
  # are always used if not match `fallback-filter.ipcidr`.
  #
  # This is a countermeasure against DNS pollution attacks.
  fallback-filter:
     geoip: true
     geoip-code: CN
     ipcidr:
       - 240.0.0.0/4
     domain:
       - '+.google.com'
       - '+.facebook.com'
       - '+.youtube.com'
  
  # Lookup domains via specific nameservers
  # nameserver-policy:
  #   'www.baidu.com': '114.114.114.114'
  #   '+.internal.crop.com': '10.0.0.1'

proxies:
  # Shadowsocks
  # The supported ciphers (encryption methods):
  #   aes-128-gcm aes-192-gcm aes-256-gcm
  #   aes-128-cfb aes-192-cfb aes-256-cfb
  #   aes-128-ctr aes-192-ctr aes-256-ctr
  #   rc4-md5 chacha20-ietf xchacha20
  #   chacha20-ietf-poly1305 xchacha20-ietf-poly1305
  - name: "ss1"
    type: ss
    server: server
    port: 443
    cipher: chacha20-ietf-poly1305
    password: "password"
    # udp: true

  - name: "ss2"
    type: ss
    server: server
    port: 443
    cipher: chacha20-ietf-poly1305
    password: "password"
    plugin: obfs
    plugin-opts:
      mode: tls # or http
      # host: bing.com

  - name: "ss3"
    type: ss
    server: server
    port: 443
    cipher: chacha20-ietf-poly1305
    password: "password"
    plugin: v2ray-plugin
    plugin-opts:
      mode: websocket # no QUIC now
      # tls: true # wss
      # skip-cert-verify: true
      # host: bing.com
      # path: "/"
      # mux: true
      # headers:
      #   custom: value

  # vmess
  # cipher support auto/aes-128-gcm/chacha20-poly1305/none
  - name: "vmess"
    type: vmess
    server: server
    port: 443
    uuid: uuid
    alterId: 32
    cipher: auto
    # udp: true
    # tls: true
    # skip-cert-verify: true
    # servername: example.com # priority over wss host
    # network: ws
    # ws-opts:
    #   path: /path
    #   headers:
    #     Host: v2ray.com
    #   max-early-data: 2048
    #   early-data-header-name: Sec-WebSocket-Protocol

  - name: "vmess-h2"
    type: vmess
    server: server
    port: 443
    uuid: uuid
    alterId: 32
    cipher: auto
    network: h2
    tls: true
    h2-opts:
      host:
        - http.example.com
        - http-alt.example.com
      path: /
  
  - name: "vmess-http"
    type: vmess
    server: server
    port: 443
    uuid: uuid
    alterId: 32
    cipher: auto
    # udp: true
    # network: http
    # http-opts:
    #   # method: "GET"
    #   # path:
    #   #   - '/'
    #   #   - '/video'
    #   # headers:
    #   #   Connection:
    #   #     - keep-alive

  - name: vmess-grpc
    server: server
    port: 443
    type: vmess
    uuid: uuid
    alterId: 32
    cipher: auto
    network: grpc
    tls: true
    servername: example.com
    # skip-cert-verify: true
    grpc-opts:
      grpc-service-name: "example"

  # socks5
  - name: "socks"
    type: socks5
    server: server
    port: 443
    # username: username
    # password: password
    # tls: true
    # skip-cert-verify: true
    # udp: true

  # http
  - name: "http"
    type: http
    server: server
    port: 443
    # username: username
    # password: password
    # tls: true # https
    # skip-cert-verify: true
    # sni: custom.com

  # Snell
  # Beware that there's currently no UDP support yet
  - name: "snell"
    type: snell
    server: server
    port: 44046
    psk: yourpsk
    # version: 2
    # obfs-opts:
      # mode: http # or tls
      # host: bing.com

  # Trojan
  - name: "trojan"
    type: trojan
    server: server
    port: 443
    password: yourpsk
    # udp: true
    # sni: example.com # aka server name
    # alpn:
    #   - h2
    #   - http/1.1
    # skip-cert-verify: true

  - name: trojan-grpc
    server: server
    port: 443
    type: trojan
    password: "example"
    network: grpc
    sni: example.com
    # skip-cert-verify: true
    udp: true
    grpc-opts:
      grpc-service-name: "example"

  - name: trojan-ws
    server: server
    port: 443
    type: trojan
    password: "example"
    network: ws
    sni: example.com
    # skip-cert-verify: true
    udp: true
    # ws-opts:
      # path: /path
      # headers:
      #   Host: example.com

  # ShadowsocksR
  # The supported ciphers (encryption methods): all stream ciphers in ss
  # The supported obfses:
  #   plain http_simple http_post
  #   random_head tls1.2_ticket_auth tls1.2_ticket_fastauth
  # The supported supported protocols:
  #   origin auth_sha1_v4 auth_aes128_md5
  #   auth_aes128_sha1 auth_chain_a auth_chain_b  
  - name: "ssr"
    type: ssr
    server: server
    port: 443
    cipher: chacha20-ietf
    password: "password"
    obfs: tls1.2_ticket_auth
    protocol: auth_sha1_v4
    # obfs-param: domain.tld
    # protocol-param: "#"
    # udp: true


proxy-providers:
  provider1:
    type: http
    url: "url"
    interval: 3600
    path: ./provider1.yaml
    health-check:
      enable: true
      interval: 600
      # lazy: true
      url: http://www.gstatic.com/generate_204
  test:
    type: file
    path: /test.yaml
    health-check:
      enable: true
      interval: 36000
      url: http://www.gstatic.com/generate_204

proxy-groups:
  - name: PROXY
    type: select
    proxies:
      - trojan
    use:
      - proxy-group

rule-providers:
  reject:
    type: http
    behavior: domain
    url: "https://raw.fastgit.org/Loyalsoldier/clash-rules/release/reject.txt"
    path: ./ruleset/reject.yaml
    interval: 86400

  icloud:
    type: http
    behavior: domain
    url: "https://raw.fastgit.org/Loyalsoldier/clash-rules/release/icloud.txt"
    path: ./ruleset/icloud.yaml
    interval: 86400

  apple:
    type: http
    behavior: domain
    url: "https://raw.fastgit.org/Loyalsoldier/clash-rules/release/apple.txt"
    path: ./ruleset/apple.yaml
    interval: 86400

  google:
    type: http
    behavior: domain
    url: "https://raw.fastgit.org/Loyalsoldier/clash-rules/release/google.txt"
    path: ./ruleset/google.yaml
    interval: 86400

  proxy:
    type: http
    behavior: domain
    url: "https://raw.fastgit.org/Loyalsoldier/clash-rules/release/proxy.txt"
    path: ./ruleset/proxy.yaml
    interval: 86400

  direct:
    type: http
    behavior: domain
    url: "https://raw.fastgit.org/Loyalsoldier/clash-rules/release/direct.txt"
    path: ./ruleset/direct.yaml
    interval: 86400

  private:
    type: http
    behavior: domain
    url: "https://raw.fastgit.org/Loyalsoldier/clash-rules/release/private.txt"
    path: ./ruleset/private.yaml
    interval: 86400


  telegramcidr:
    type: http
    behavior: ipcidr
    url: "https://raw.fastgit.org/Loyalsoldier/clash-rules/release/telegramcidr.txt"
    path: ./ruleset/telegramcidr.yaml
    interval: 86400

  cncidr:
    type: http
    behavior: ipcidr
    url: "https://raw.fastgit.org/Loyalsoldier/clash-rules/release/cncidr.txt"
    path: ./ruleset/cncidr.yaml
    interval: 86400

  lancidr:
    type: http
    behavior: ipcidr
    url: "https://raw.fastgit.org/Loyalsoldier/clash-rules/release/lancidr.txt"
    path: ./ruleset/lancidr.yaml
    interval: 86400

  applications:
    type: http
    behavior: classical
    url: "https://raw.fastgit.org/Loyalsoldier/clash-rules/release/applications.txt"
    path: ./ruleset/applications.yaml
    interval: 86400


rules:
  - RULE-SET,applications,DIRECT
  - RULE-SET,private,DIRECT
  - RULE-SET,reject,REJECT
  - RULE-SET,icloud,DIRECT
  - RULE-SET,apple,DIRECT
  - RULE-SET,google,DIRECT
  - RULE-SET,proxy,PROXY
  - RULE-SET,direct,DIRECT
  - RULE-SET,lancidr,DIRECT
  - RULE-SET,cncidr,DIRECT
  - RULE-SET,telegramcidr,PROXY
  - GEOIP,LAN,DIRECT
  - GEOIP,CN,DIRECT
  - MATCH,PROXY
~~~

### 使用教程

对于windows用户，如果tun.stack选择了system（我是使用system），我们需要去防火墙开放clash程序的权限，参考这个[博文](https://blog.muted.top/2021/11/06/%E5%85%B3%E4%BA%8E%E4%BD%BF%E7%94%A8Clash%E7%9A%84TUN%E9%80%8F%E6%98%8E%E4%BB%A3%E7%90%86%E9%9C%80%E8%A6%81%E7%9F%A5%E9%81%93%E7%9A%84%E4%B8%80%E5%88%87/#tun-%E6%A8%A1%E5%BC%8F%E7%9A%84%E4%B8%A4%E4%B8%AA%E5%8D%8F%E8%AE%AE%E6%A0%88)。


根据自己代理类型来填写好Proxy，机场用户可以使用proxy-provider，他会获取机场代理文件中的代理配置为自己所用，而且无视机场的其他配置，proxy-provider支持http和文件。

proxy-group的配置可以参考clash官方[wiki](https://github.com/Dreamacro/clash/wiki)和一个第三方的[gitbook](https://lancellc.gitbook.io/clash)

对于分流规则rule，可参考这个[GitHub仓库](https://github.com/Loyalsoldier/clash-rules)，也可以直接用我的。

对于clash的面板我选择了使用[yacd](https://github.com/haishanh/yacd)，注意要修改配置文件中的external-ui为控制面板文件的地址，随后你就可以访问127.0.0.1:9090/ui(如果没有修改external-controller)

最好使用时把clash主程序，wintun.dll(windows需要)，配置文件，面板和clash配置的外部资源都放在一个目录，建一两个子目录分类管理。

开始配置的时候最好使用命令行手动启动，看看日志输出，能够及时发现问题。

配置好后 windows 用户我推荐使用powershell隐藏窗口启动。

启动：
~~~
 Start-Process -FilePath "path\to\clash.exe" -ArgumentList "-d","path\to\directory" -Verb RunAs -WindowStyle Hidden
~~~

关闭：

1. 获取clash的pid
~~~ 
Get-Process clash
~~~

2. 关闭clash（需要管理员权限）

~~~
 Stop-Process ${pid} -confirm
~~~

输入后会问你是否终止clash，（确定，输入Y），可以看看程序名是不是clash，防止记错pid关错程序