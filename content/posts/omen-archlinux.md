---
title: "暗影精灵8安装Archlinux"
date: 2022-03-19T11:21:01+08:00
draft: true
slug: Omen-Archlinux
---

## 系统安装

暗影精灵系统安装与一般笔记本安装Archlinux没有什么区别，我是按照知乎上面的[双系统教程](https://zhuanlan.zhihu.com/p/138951848)来做的。

我和他的主要区别是我选择了使用BtrFS来作为主文件系统，就是馋它的快照和子卷功能，对于swap我选择了划一个swap分区，教程里面选择使用swap文件。

## 硬件驱动

安装完系统后，网卡和显卡都是可以驱动的，最新的wif6E网卡也是可以完美驱动的，但是电脑没用声音，显示没有声音输出设备。

通过翻阅ArchWIKI，发现这个声卡问题应该是暗影精灵8的声卡太新造成的，安装sof-firmware和alsa-ucm-conf这2个固件即可解决。

![ALSA](https://img.nobody404.xyz/img/ALSA.webp)

## 日常使用

### ArchLinuxCN
添加ArchlinuxCN源，它里面有许多我们中国人日常使用的程序。

不过他的keyring可能无法安装，这个通过这个[办法](https://www.archlinuxcn.org/gnupg-2-1-and-the-pacman-keyring/)来解决。


### Fcitx5-rime
日常使用当然少不了中文输入法，我推荐使用fcitx5-rime，通过fcitx5-im软件包组和fcitx5-rime即可，不过rime他默认没有词典，输入体验很差，推荐看这个[教程](https://zhuanlan.zhihu.com/p/287774005)自己添加词典。

因为默认的luna_pinyin是繁体的，我们需要开启繁转简，通过F4打开选项面板，在点2再选择4，开启繁转中，操作可看[wiki](https://wiki.archlinux.org/title/Rime_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#%E4%BD%BF%E7%94%A8)，这样就可以畅快的输入中文简体了。

还有一个问题就是fcitx5可能无法emoji，需要安装带有 Emoji 的字体，具体看[ArchWiki](https://wiki.archlinux.org/title/Fcitx5_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

### 视频硬件解码

对于视频硬解，我们只按照[Hardware_video_acceleration](https://wiki.archlinux.org/title/Hardware_video_acceleration)来弄即可。

播放器的话VLC是可以自动识别系统能否进行硬解，如果可以自动开启视频硬解。

浏览器的话chromium说是可以硬解，但我就是不成功，firefox按照[wiki](https://wiki.archlinux.org/title/Firefox#Hardware_video_acceleration)操作是可以硬解的。