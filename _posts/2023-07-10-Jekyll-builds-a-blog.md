---
title: 使用 Jekyll 搭建博客
date: 2023-07-10 00:34:00 +0800
categories: [Blogging]
tags: [Jekyll]
---

Jekyll 是一个免费的开源静态网站生成器，主要用于将纯文本转换为静态的 HTML、CSS 和 JavaScript 文件，从而生成完整的静态网站。
Jekyll 官网：[https://jekyllrb.com/](https://jekyllrb.com/)

## 1.安装前提
Jekyll 支持大多数操作系统，依赖如下：
- Ruby 2.5.0+
- RubyGems
- GCC和Make

## 2.安装 Ruby
本文环境为 Windows 操作系统，通过 RubyInstaller 进行安装 Ruby，前往 [RubyInstaller 下载地址](https://rubyinstaller.org/downloads/)，下载相应的 Ruby 安装程序（例如，Ruby+Devkit 2.6.6 (x64) - 对于64位系统），然后运行安装程序，确保在安装过程中选中`Add Ruby executables to your PATH`以便于在命令行中使用 Ruby。

在弹出的命令行窗口中选择`MSYS2 and MINGW development tool chain`。
![](img/msys2-installation-choice.png)

检查是否安装成功：
```shell
$ ruby -v
$ gem -v
```

## 3.安装 Jekyll
打开新的命令行，使用 gem 安装 Jekyll 和 Bundler。
```shell
$ gem install jekyll bundler
```
检查是否安装成功：
```shell
$ jekyll -v
```

## 4.Jekyll 基础


