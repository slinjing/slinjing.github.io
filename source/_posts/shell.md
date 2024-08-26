---
title: Shell脚本基础  # 必选-文章标题
date: 2024-05-05 00:00:00    # 必选-文章创建日期
updated:           # 可选-文章更新日期
tags:              # 可选-文章标   
  - Linux       
  - Shell
categories: Shell  # 可选-文章分类
keywords:       # 可选-文章关键字
description:    # 可选-文章描述
top_img:        # 可选-文章顶部图片
comments:       # 可选-显示文章评论模块(默认 true)
cover:  '/img/shell.jpg' # 可选-文章缩略图(如果没有设置top_img,文章页顶部将显示缩略图，可设为false/图片地址/留空)
---

## 概述



Shell是一个命令行解释器，它能够接收应用程序或用户命令，然后调用操作系统内核；同时也是一个功能强大的脚本语言。
创建Shell脚本时，第一行必须指定要使用的Shell，格式为：`#!/bin/bash`，当然也可以使用其他的Shell，Linux系统中提供如以下几种：
```shell
$ cat /etc/shells
/bin/sh
/bin/bash
/usr/bin/sh
/usr/bin/bash
```

在指定了shell之后，就可以在文件的每一行中输入命令，注释可用#添加。脚本命名建议以.sh结尾。下面为一个简单的脚本：
```shell
#!/bin/bash
echo "hello, world"
```

## 执行方式
执行脚本有多种方式，首先将上面的内容保存为hello.sh
>方式1：
```shell
$ sh hello.sh  # 可以使用任意一种shell
hello, world
```

>方式2：
```shell
source hello.sh
hello, world
```

>方式3：
```shell
$ chmod u+x hello.sh
$ ./hello.sh  
hello, world
```

## 变量
在Shell编程中，变量是用于存储数据值的名称，分为环境变量和用户变量。

### 变量命名
变量的命名等号左右两边不能有空格，变量名的命名须遵循规则：只包含字母、数字和下划线；不能以数字开头；避免使用Shell关键字；使用大写字母表示常量；避免使用特殊符号；避免出现空格。正确变量定义如下：
```shell
name="tom"
age=18
```

### 使用变量
使用变量可以用下面两种方式：
```shell
echo $name

skill=shell
echo "$name is good at ${skill}Script"
```

### 只读变量
使用readonly命令可以将变量定义为只读变量，只读变量的值不能被改变。
```shell
#!/bin/bash
mypath="/home"
readonly mypath
```



环境变量用来记录特定的系统信息可以使用`set`命令获取当前环境变量列表：
```shell
$ set
BASH=/bin/bash
BASHOPTS=checkwinsize:cmdhist:expand_aliases:extquote:force_fignore:histappend:hostcomplete:interactive_comments:login_shell:progcomp:promptvars:sourcepath
BASH_ALIASES=()
BASH_ARGC=()
BASH_ARGV=()
BASH_CMDS=()
BASH_LINENO=()
BASH_SOURCE=()
BASH_VERSINFO=([0]="4" [1]="2" [2]="46" [3]="2" [4]="release" [5]="x86_64-redhat-linux-gnu")
BASH_VERSION='4.2.46(2)-release'
COLUMNS=169
DIRSTACK=()
EUID=0
GROUPS=()
HISTCONTROL=ignoredups
HISTFILE=/root/.bash_history
HISTFILESIZE=1000
HISTSIZE=1000
HOME=/root
HOSTNAME=docker
HOSTTYPE=x86_64
......
```

除了环境变量外，通过会使用自定义的变量。变量定义通过等号将值赋给变量，等号两边不能出现空格，如下：
```shell
a=10
b=test
c="hello world"
```
变量使用时在变量名前加$符号，如果要撤销一个变量可以使用`unset 变量名`。
```shell
#!/bin/bash
a=10
echo a=$a

# 撤销变量
unset a
echo a=$a
```

>静态变量定义
```shell
#!/bin/bash
readonly b=20
echo $b
```

