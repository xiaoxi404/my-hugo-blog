---
title: "The Shell"
date: 2022-03-19T16:04:58+08:00
draft: false
slug: The-Shell
---

这次的的课程主要讲了shell的使用，[课程地址](https://missing-semester-cn.github.io/2020/course-shell/)。

### shell常用命令

| 命令 | 用途 |
| --- | --- |
| echo | 打印参数 |
| cd | 切换目录 |
| ls | 打印当前目录下的文件 |
| pwd | 获取当前工作目录 |
| mv | 重命名文件或移动文件 |
| cp | 拷贝文件 |
| mkdir | 新建文件夹 |
| **man** | 展示命令的文档 |

### 在程序间创建连接

**< file** 和 **> file**, 这两个命令将程序的输入输出分别重定向到文件。

**\>\>** 向文件追加内容。

**\|** 将一个程序的输出和另外一个程序的输入连接起来。

### 文件权限

例如：

~~~
missing:~$ ls -l /home
drwxr-xr-x 1 missing  users  4096 Jun 15  2019 missing
~~~ 

d 代表目录

rwx 代表当前用户可读（r）可写（w）可执行or该文件夹以及其父文件夹的“搜索”权限（x）

r-x 代表当前用户组可读（r）可执行or该文件夹以及其父文件夹的“搜索”权限（x）

r-x 代表其他人可读（r）可执行or该文件夹以及其父文件夹的“搜索”权限（x）

### 课后练习题

~~~
1. cd /tmp
2. mkdir missing
3. cd missing
4. man touch
5. touch semester
6. echo '#!/bin/sh' > semester
7. echo 'curl --head --silent https://missing.csail.mit.edu' >> semester 
8. man chmod
9. chmod +x semester
10. ./semester
11. ./semester | grep --ignore-case last-modified | cut --delimiter=' ' -f2- > last-modiffied.txt

12. cd /sys/class/power_supply/BAT0
13. cat capacity
~~~