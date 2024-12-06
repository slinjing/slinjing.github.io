---
title: Docker Compose 安装
date: 2022-12-12 11:33:00 +0800
categories: [Docker]
tags: [Docker]
---

Docker Compose 是 Docker 官方的开源项目，负责实现对 Docker 容器集群的快速编排，开源地址：<https://github.com/docker/compose>。


## 1.二进制
从 [官方 GitHub Release](https://github.com/docker/compose/releases) 处直接下载编译好的二进制文件，然后赋予执行权限即可。
```shell
$ sudo curl -L https://github.com/docker/compose/releases/download/1.27.4/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

# 国内用户可以使用以下方式加快下载
$ sudo curl -L https://download.fastgit.org/docker/compose/releases/download/1.27.4/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```

## 2.PIP
x86_64 架构的 Linux 建议下载二进制包进行安装，如果计算机的架构是 ARM (例如，树莓派)，再使用 pip 安装。这种方式是将 Compose 当作一个 Python 应用来从 pip 源中安装。
```shell
$ sudo pip install -U docker-compose
```

可以看到类似如下输出，说明安装成功。
```shell
Collecting docker-compose
  Downloading docker-compose-1.27.4.tar.gz (149kB): 149kB downloaded
...
Successfully installed docker-compose cached-property requests texttable websocket-client docker-py dockerpty six enum34 backports.ssl-match-hostname ipaddress
```

## 3.补全命令
```shell
$ curl -L https://raw.githubusercontent.com/docker/compose/1.27.4/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose
```

## 4.卸载
二进制包方式安装的，删除二进制文件即可。
```shell
$ sudo rm /usr/local/bin/docker-compose
```
通过 pip 安装的，则执行如下命令即可删除。
```shell
$ sudo pip uninstall docker-compose
```