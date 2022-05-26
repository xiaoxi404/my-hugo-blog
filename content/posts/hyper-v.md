---
title: "vscode 使用 Hyper-V 的虚拟机进行伪远程开发"
date: 2022-05-26T15:54:20+08:00
draft: false
slug: vscode-hyper-v
---

## 背景

最近在学习字节开源的kitex，感觉它各方面都挺好，但是它有一个问题，这个框架无法在windows下使用和开发，官方文档推荐使用wsl，但是wsl它有个非常令人讨厌的地方，那就是wls他只吃内存不吐内存。因为这个问题，所以我选择不在自己的windows上使用docker-desktop和wsl。

面对这个情况，我剩下的选择只剩下本地虚拟机和远程开发了。因为正好有一个腾讯云的服务器吃灰，所以我选择了远程开发这条路，远程开发也有2个选择，vscode 的 remote-ssh 和 JB 家的 remote development，我最终选择了vscode，因为感觉JB家这个服务器配置要求有点高，需要2g内存，而且有点麻烦，需要装个JetBrains Gateway。vscode只需要一个插件和ssh公钥配置好即可。

## vscode remote-ssh 体验

我感觉使用体验已经超越本地开发了。首先，vscode的丰富的插件都是可以安装到服务器的，这里疯狂安利vscode的docker插件，管理docker太方便了，安装一个docker插件就可以控制服务器的docker。其次，文件打开编辑体验基本和本地开发一样。最后，vscode的生态好，go的插件体验很好，感觉替代goland来开发go完全可以。

## 为什么还要在本地开虚拟机进行伪远程开发

首先，远程开发虽好，但是没网不就凉了。感觉日常碰到没网的情况还是很常见的，所以还是本地要有个环境。

其次，虚拟机方便对环境进行备份。环境配好，把虚拟机导出，把文件管理好，以后环境坏了，我再倒回去。

最后，虚拟机的快照，还原点功能，可以很方便地还原系统。

## 为什么选择Hyper-V

对于绝大多数的人来说，提到虚拟机就想到VMware WorkStation，少数人还会想到VirtualBox。

他们都很优秀，我过去一直选择使用VirtualBox（没别的原因，开源，轻量）。

但是我最终没有选择它，主要因为Hyper-V的架构非常特别，windows启用Hyper-V后，windows自己也会变成一个拥有硬件控制权的虚拟机，和Vmware和ESXi相似，具体可看这篇[文章](https://zhuanlan.zhihu.com/p/427870520)。其次是Hyper-V支持[动态内存](https://docs.microsoft.com/zh-cn/windows-server/virtualization/hyper-v/supported-ubuntu-virtual-machines-on-hyper-v)，意思是如果虚拟机空闲，就少给他点内存，虚拟机要干重活了，就多给它分配内存，当然这需要寄主操作系统的支持。

## Hyper-V的配置

Hyper-V的开启和操作系统的安装网上教程很多，windows家庭版是不能开启的，自行百度吧。

由于Hyper-V的需要寄生操作系统内核支持，选择系统时看看微软的文档，我喜欢用Ubuntu，文档里就提到了一些重要信息。

Hyper-V的网络配置默认提供了外部虚拟交换机（桥接），内部虚拟交换机（host-only）和专用虚拟交换机（仅虚拟机间互联）。

hyper-V虚拟机想要连接外网，可以直接选择最简单的nat和外部虚拟交换机。

但是选择外部虚拟交换机的话，会占用网卡，我使用Clash tun模式影响虚拟机。

选择内部虚拟交换机和网卡进行网络共享的话，clash的tun模式无法使用。

clash tun不能使用这不能忍啊。最后我想到的办法是，nat（默认虚拟交换机）和内部虚拟交换机结合使用，前者提供外网访问，后者提供寄主与寄生系统之间的网络连接。这意味着每台虚拟机有2张网卡，一个nat一个内部使用。这么做的好处是可以利用clash富强，还解决了nat下宿主无法直连虚拟机的问题。

网络配置：
1. 虚拟机配置

![vm config](https://img.nobody404.xyz/img/Hyper-v_1.webp)

![vswitch config](https://img.nobody404.xyz/img/Hyper-v_2.webp)

2. 网卡ip配置

windows 网卡配置

![windows interface config](https://img.nobody404.xyz/img/Hyper-v_3.webp)

linux 网卡配置

![linux interface config](https://img.nobody404.xyz/img/Hyper-v_4.png)

**重点放开防火墙，否则宿主和寄生操作系统无法通行**

具体操作可看这篇[文章](https://cn-support.waters.com/KB_Inf/MassLynx/WKB58216_How_to_apply_the_Windows_firewall_settings_to_just_one_network_card),把 vEthernet (Local Switch) （即内部虚拟交换机名字的网卡）放行，并且需要在private，public，Domain profile中都放行，如果还是不行的话，就关闭防火墙吧。

3. 操作完成后测试

![测试截图](https://img.nobody404.xyz/img/Hyper-v_5.png)

windows上clash开启tun模式，可以富强
![测试截图2](https://img.nobody404.xyz/img/Hyper-v_6.png)

# 总结

配置基本就是这样，就可以使用windows上的vscode连到虚拟机进行远程开发体验了。