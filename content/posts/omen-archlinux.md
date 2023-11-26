---
title: "暗影精灵8安装Archlinux"
date: 2022-03-19T11:21:01+08:00
draft: false
slug: Omen-Archlinux
---

## 系统安装

暗影精灵系统安装与一般笔记本安装Archlinux没有什么区别，我参考了知乎文章[2021 Archlinux双系统安装教程](https://zhuanlan.zhihu.com/p/138951848)和[ArchWiki的安装教程](https://wiki.archlinux.org/title/Installation_guide)，对于教程中不认识的command和package建议查一下ArchWiki。

我选择了使用BtrFS来作为主文件系统，并且使用[snapper](https://wiki.archlinux.org/title/Snapper)来定期对系统盘定时做快照。

## 硬件驱动

安装完系统后，网卡和显卡都是可以驱动的，最新的wif6E网卡也是可以完美驱动的，但是电脑没有声音，显示没有声音输出设备。

翻阅Alsa的[ArchWIKI](https://wiki.archlinux.org/title/Advanced_Linux_Sound_Architecture)后发现是缺少firmware，安装sof-firmware和alsa-ucm-conf这2个package即可。

## 日常使用

### ArchLinuxCN

添加ArchlinuxCN源，它里面有许多官方库里面缺少的package。

如果ArchlinuxCN的keyring安装出现问题，可以尝试[ArchlinuxCN的这一公告](https://www.archlinuxcn.org/gnupg-2-1-and-the-pacman-keyring/)来解决。


### Fcitx5-rime

日常使用当然少不了中文输入法，我推荐使用fcitx5-rime，通过fcitx5-im软件包组和fcitx5-rime即可，不过rime他默认没有词典，输入体验很差，推荐看这个[教程](https://zhuanlan.zhihu.com/p/287774005)自己添加词典。

rime配置建议参考[可能只适合我自己的 RIME 配置](https://sh.alynx.one/posts/My-RIME/)和[可能只适合我自己的 RIME 配置 2](https://sh.alynx.one/posts/My-RIME-2/)。

---

因为默认的luna_pinyin是繁体的，我们需要开启繁转简，通过F4打开选项面板，在点2再选择4，开启繁转中，操作可看[wiki](https://wiki.archlinux.org/title/Rime_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#%E4%BD%BF%E7%94%A8)，这样就可以畅快的输入中文简体了。

还有一个问题就是fcitx5可能无法emoji，需要安装带有 Emoji 的字体，具体看[ArchWiki](https://wiki.archlinux.org/title/Fcitx5_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

### 视频硬件解码

对于视频硬解，我们只按照[Hardware_video_acceleration](https://wiki.archlinux.org/title/Hardware_video_acceleration)来弄即可。

播放器的话VLC是可以自动识别系统能否进行硬解，如果可以自动开启视频硬解。

浏览器的话chromium说是可以硬解，但我就是不成功，firefox按照[wiki](https://wiki.archlinux.org/title/Firefox#Hardware_video_acceleration)操作是可以硬解的。

### 字体配置

字体配置推荐阅读[Linux fontconfig 的字体匹配机制](https://catcat.cc/post/2020-10-31/)和[用 fontconfig 治理 Linux 中的字体](https://catcat.cc/post/2021-03-07/)