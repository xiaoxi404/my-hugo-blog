---
title: "Shell Tools"
date: 2022-03-21T11:20:35+08:00
draft: false
slug: Shell-Tools
---

这次[课程](https://missing-semester-cn.github.io/2020/shell-tools/)主要学习shell工具和脚本。

## shell脚本
shell都有着自己的一套脚本语言，包括变量、控制流和自己的语法。本次我们专注与bash脚本。

变量赋值：fool=bar

访问变量：$foo

foo = bar （使用空格隔开）是不能正确工作的， 因为解释器会调用程序 foo 并将 = 和 bar 作为参数

字符串通过 '（原义字符串）和 "（转义）分隔

bash 支持 if，case， while 和 for 这些控制流关键字。

bash 支持函数。

例如：
~~~
mcd () {
    mkdir -p "$1"
    cd "$1"
}
~~~

- $0 - 脚本名
- $1 到 $9 - 脚本的参数。 $1是第一个参数，以此类推
- $@ - 所有参数
- $# - 参数个数
- $? - 请一个命令的返回值
- $$ - 当前脚本的进程识别码
- !! - 完整的上条命令，包括参数。常见应用：当你因为权限不足执行命令失败时，可以使用 sudo !! 在尝试一次
- $_ - 上一条命令的最后一个参数。如果你正在使用的是交互式 shell，你可以通过按下 Esc 之后键入 . 来获取这个值。

返回码或退出状态是脚本\命令间交流状态的方式。返回值 0 表示正常执行，其他所有非 0 的返回值都表示有错误发生。

退出码可以搭配 && （与操作符） 和 || （或操作符）使用。

~~~
false || echo "Oops, fail"
# Oops, fail

true || echo "Will not be printed"
#

true && echo "Things went well"
# Things went well

false && echo "Will not be printed"
#

false ; echo "Th #!/usr/bin/env bash

 n=$(( RANDOM % 100 ))

 if [[ n -eq 42 ]]; then
    echo "Something went wrong"
    >&2 echo "The error was using magic numbers"
    exit 1
 fi

 echo "Everything went according to plan"is will always run"
# This will always run

~~~

以$(CMD)方式来执行CMD这个命令时，他的输出结果会替换掉$(CMD)，它还有个特性进程替换，<(CMD) 会执行CMD并将结果输出到一个临时文件中，并将以<(CMD)替换为临时文件名。这在我们希望返回值通过文件而不是STDIN传递时很有用。例如， diff <(ls foo) <(ls bar) 会显示文件夹 foo 和 bar 中文件的区别。

在bash中尽量使用 [[ ]] 而不是 ［］。

shell的通配：

- 通配符 - ? 和 * 来匹配一个或任意隔字符
- { } - 当一系列指令包含一段公共字串时，{ }自动展开命令
  
## shell工具

[TLDR pages](https://tldr.sh/) 提供样例帮助我们快速找到正确选项。

查找文件：find，替代品：fd

查找代码：grep

查找shell命令：history

文件夹导航：fasd和autojump

### 课后练习题

1.
~~~
ls -h -a --color -lt -u
~~~

3.
~~~
#!/usr/bin/env bash

num=0

(
until false ; do {
    echo $num > nums.txt
    source wrong.sh >> result.txt 2>&1
    ((num++))
}
done
)
num=$(<nums.txt)
echo "错误次数为 $num" >> result.txt

~~~

4.
~~~
find ./test -type f -name '*.html' | xargs -d '\n' 7z a html.zip
~~~

5.
不会
答案解析：
~~~
find . -type f | xargs ls -lt | head -n 1
~~~