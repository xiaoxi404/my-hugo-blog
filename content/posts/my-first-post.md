---
title: "Hugo博客搭建"
date: 2022-01-01T15:03:42+08:00
draft: true
---

# Hugo 博客的搭建 
## 安装Hugo
因为hugo是用go编写的，所以安装它只需要将他的二进制文件放入系统路径中去即可

## 创建blog项目和添加主题
创建博客，使用下面这一命令，{your blog name}为你的blog名字
~~~
hugo new site {your blog name}
~~~
添加主题(这里以Ananke为例)，hugo的[主题商店](https://themes.gohugo.io)
~~~
git aubmodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
~~~
## 开始写博客文章
{category name} 是你的文章类别，{title}你的文章题目，注意你选择了一个主题后需要根据他的示例配置文件来进行修改config.toml(其在更目录下)
~~~
hugo new {category name}/{title}
~~~
# 建议
可以考虑使用github与cloudflare pages搭配使用，每次写好博客push到github上去就cloudflare pages就能够自动部署，这个可以看cloudflare的[官方文档](https://developers.cloudflare.com/pages/framework-guides/deploy-a-hugo-site)。


如果和我一样选择使用cloudflare pages需要注意的是如果你项目根目录中没有.gitmodules文件，cloudflare会部署失败(clone project处)，也是看了这个[帖子](https://community.cloudflare.com/t/error-when-deploying-to-cloudfare-pages/295522)才发现问题，哎哎错怪了cloudflare好几个月