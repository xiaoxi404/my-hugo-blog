---
title: "The Shell"
date: 2022-03-19T16:04:58+08:00
draft: false
slug: The-Shell
---

这次的的课程主要讲了shell的使用，[课程地址](https://missing-semester-cn.github.io/2020/course-shell/)。

### shell基本介绍

shell输入带有空格的字符串时使用\，" 或 ' 。

环境变量(Environment Variable)： PATH(shell寻找程序时所查找的目录)

cd - : 跳转到你所处的上一个目录

大多数程序都实现了--help（flag），用它来查看帮助程序的帮助信息，[]代表可选的，...代表不填or一个or更多。

ctrl + l：清空终端，让光标回到顶部。

xdg-open：使用合适的程序打开文件（windows下用start）

| 命令 | 用途 |
| --- | --- |
| which | 告诉我们运行的程序具体是哪个目录的 |
| echo | 打印参数 |
| cd | 切换目录 |
| ls | 打印当前目录下的文件 |
| pwd | 获取当前工作目录 |
| mv | 重命名文件或移动文件 |
| cp | 拷贝文件 |
| mkdir | 新建文件夹 |
| rmdir | 删除空目录 |
| **man** | 展示命令的文档 |

### 在程序间创建连接

流（stream）：

shell默认给我们的每个程序创建2个主要的流（input stream 和 output stream），

**< file** 和 **> file**（overwrite 覆写）, 这两个命令将程序的输入输出分别重定向到文件。

**\>\>**（append） 向文件追加内容。

**\|** 将一个程序的输出和另外一个程序的输入连接起来。

**\#** 以root运行整条命令。 

### 文件权限

例如：

~~~
missing:~$ ls -l /home
drwxr-xr-x 1 missing  users  4096 Jun 15  2019 missing
~~~ 

d 代表目录

文件权限：9个字母组成，3个字母一组，第一组代表文件所有者的权限，第二组代表拥有这些文件的用户组的权限，最后一组代表给非所有者的其他人的权限，r：查看，w：修改，x：执行，-：无权限。

目录权限： r：允许你查看文件里有哪些东西，w：能否重命名、新建or删除目录里面的文件，x：能否进入目录，-：无权限。

### 课后练习题

~~~
1. echo $SHELL

2.  cd /tmp
    mkdir missing
3.  man touch
4.  cd missing
    touch semester
5.  echo '#!/bin/sh' | tee semester
    echo 'curl --head --silent https://missing.csail.mit.edu' | tee semester 
6.  没有执行权限
7.  man chmod
8.  chmod u+x semester
    ./semester
9. ./semester | grep --ignore-case last-modified | cut --delimiter=' ' -f2- > last-modiffied.txt

10. cd /sys/class/power_supply/BAT0
    cat capacity
~~~
补充：

1. 非引号反斜杠（backslash）'/'是Bash的转义字符。它保留了接下来的字符的字面价值，但换行除外。如果出现了一个换行符，而反斜杠本身没有引号，那么换行符将被视为一个续行（也就是说，它将从输入流中删除，并被有效忽略）。

2. 用单引号(''')括起来的字符保留了引号内每个字符的字面价值。单引号不能出现在单引号之间，即使前面有反斜杠。

3. 用双引号('"')括起来的字符保留引号内所有字符的字面价值，除了'$','`','\'，启用历史拓展时的'!'。用'\'来进行转义，保留他们的字面价值。
