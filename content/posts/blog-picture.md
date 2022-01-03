---
title: "利用backblaze和CloudFlare来存储博客图片"
date: 2022-01-03T20:48:02+08:00
draft: false
slug: blog-picture
---
## 介绍
backbalze是一家云存储厂商，他家存储一直以便宜著称，而且他家是有免费额度的，10GB存储免费空间，每天10GB免费下载流量和2500API免费调用次数，可以说是非常良心了.它去年加入cloudflare的带宽联盟，这个联盟致力于为共同客户降低或免除数据传输，所以cloudflare的cdn访问他的对象存储的流量是免费的，而且cdn还能缓存图片，这样产生的流量和api调用就更少了，所以我们利用这一点来免费存储和分享自己的博客图片。
![BandwidthAlliance](https://img.nobody404.xyz/img/BandwidthAlliance.png)
除了blackblaze以外，阿里云也加入了这个联盟，有人也搞出来了阿里云OSS和cloudflare的白嫖图片存储的[方案](https://www.duangvps.com/archives/1793)，感兴趣的可以了解一下，我也考虑过用它，但是阿里云OSS海外版的开通需要海外手机账号，所以只能使用现在这套方案。

## 教程
我是看这篇[博客](https://www.cnblogs.com/Sky-seeker/p/14213962.html)学会的，如果感兴趣话可以看他的，按博客中的步骤来设置即可。


如果只想存储博客图片可以不看winscap和mixplore那部分内容，使用sharex基本满足了图片上传需求。


对象存储当然不仅仅是存储图片，这套方案还可以存储各种文件，这里建议打开容量和流量报警，防止流量和容量超标。

## 注意点
我刚开始按照这篇博客来设置，死活没办法通过自己的域名来访问图片，通过backbalze的域名就可以访问，结果看到backblaze的[官方博客](https://help.backblaze.com/hc/en-us/articles/217666928-Using-Backblaze-B2-with-the-Cloudflare-CDN)里面说需要设置cloudflare的SSL为full（即完全）。
![官方博客要求](https://img.nobody404.xyz/img/msedge_TDLjlTv3hj.png)


![SSL/TLS设置](https://img.nobody404.xyz/img/UbbZDZNqvy.png)