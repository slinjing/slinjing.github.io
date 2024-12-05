---
title: Docker 安装指南
date: 2022-12-11 11:33:00 +0800
categories: [Docker]
tags: [Docker]
---

‌Docker ‌是一个开源的容器化平台，旨在简化应用程序的开发、部署和运行过程。它通过将应用程序及其依赖打包到一个轻量级的、可移植的容器中，实现了应用程序的快速部署和高效运行。

## 1.CentOS 安装 Docker

### 1.1 前提条件
CentOS 操作系统安装 Docker 需要满足系统版本最低为 Centos7，Linux 内核为 3.8 以上。
```shell
$ cat /etc/redhat-release
CentOS Linux release 7.9.2009 (Core)
$ uname -r
3.10.0-1160.el7.x86_64
```

### 1.2 安装系统工具
```shell
$ yum install -y yum-utils device-mapper-persistent-data lvm2
```

### 1.3 设置存储库
由于 Docker 官方提供的 yum 仓库在国外，受网络限制比较大，这里使用阿里云提供的 yum 仓库进行安装。
```shell
$ yum install -y yum-utils
$ yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
$ sed -i 's+download.docker.com+mirrors.aliyun.com/docker-ce+' /etc/yum.repos.d/docker-ce.repo
$ yum makecache fast
```

### 1.4 安装 Docker
- 安装最新版本

```shell
$ yum -y install docker-ce
```

- 安装指定版本

```shell
$ yum list docker-ce.x86_64 --showduplicates | sort -r  # 查看可选版本
$ yum -y install docker-ce-[VERSION]  # 安装指定版本
```

## 2.Ubuntu 安装 Docker

### 2.1 前提条件
Docker 支持多种版本的 Ubuntu 操作系统，例如：Ubuntu Noble 24.04 (LTS)、Ubuntu Jammy 22.04 (LTS)、Ubuntu Focal 20.04 (LTS)、Ubuntu Bionic 18.04 (LTS)。

### 2.2 安装系统工具
```shell
$ apt-get update
$ apt-get -y install apt-transport-https ca-certificates curl software-properties-common
```

### 2.3 安装 GPG 证书
```shell
$ curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
```

### 2.4 添加软件源
```shell
$ add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```

### 2.5 安装 Docker

- 安装最新版本

```shell
$ apt-get -y update
$ apt-get -y install docker-ce
```

- 安装指定版本

```shell
$ apt-cache madison docker-ce  # 查看可选版本
$ apt-get -y install docker-ce=[VERSION]  # 安装指定版本
```

## 3.Windows 安装 Docker

### 3.1 前提条件
[Docker Desktop for Windows](https://docs.docker.com/desktop/setup/install/windows-install/) 支持 64 位版本的 Windows 10 Pro，且必须开启 Hyper-V，或者 64 位版本的 Windows 10 Home v1903 及以上版本。

### 3.2 安装 Docker
- 手动安装

点击 [链接](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe) 下载 Docker Desktop for Windows，下载好之后双击 Docker Desktop Installer.exe 开始安装。

- winget 安装

```shell
$ winget install Docker.DockerDesktop
```

### 3.3 运行 Docker
在 Windows 搜索栏输入 Docker 点击 Docker Desktop 开始运行，Docker 启动之后会在 Windows 任务栏出现鲸鱼图标。当鲸鱼图标静止时，说明 Docker 启动成功，之后可以打开 PowerShell 使用 Docker。

## 4.启动 Docker
```shell
$ systemctl enable docker --now
```

## 5.镜像加速
Docker 默认的镜像下载源因为地理位置、网络速度等原因拉取镜像较慢，使用阿里云的 Docker 镜像加速服务。
[阿里云](https://www.aliyun.com/?spm=5176.12901015-2.0.0.5a35525chieqsr) -> 点击管理控制台 -> 登录账号(淘宝账号) -> 左侧镜像工具 -> 镜像加速器 -> 复制加速器地址。

### 5.1 Linux
修改 Docker 的配置文件 `/etc/docker/daemon.json`，如果文件不存在新建该文件，添加以下内容：
```shell
{  
"registry-mirrors": ["https://b81vthfh.mirror.aliyuncs.com"]
}
```

修改完成后重新启动服务。
```shell
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

### 5.2 Windows
在任务栏托盘 Docker 图标内右键菜单选择 Change settings，打开配置窗口后在左侧导航菜单选择 Docker Engine，在右侧像下边一样编辑 json 文件，之后点击 Apply & Restart 保存后 Docker 就会重启并应用配置的镜像地址了。
```shell
{  
"registry-mirrors": ["https://b81vthfh.mirror.aliyuncs.com"]
}
```

### 5.3 验证
执行 `docker info` 命令，返回如下内容，说明配置成功。
```shell
Registry Mirrors:
 https://b81vthfh.mirror.aliyuncs.com/
```

## 6.修改存储路径
使用 `docker info | grep Dir` 命令，可以看到 Docker 的默认存储路径是 /var/lib/docker，可以按照以下流程进行更改。

- 停止 Docker

```shell
$ systemctl stop docker
```

- 修改 daemon.json 文件

```shell
{  
"registry-mirrors": ["https://b81vthfh.mirror.aliyuncs.com"],
"data-root": "/home/docker"  
}
```

> /home/docker 为新存储路径，它必须存在。
{: .prompt-info }
<!-- markdownlint-restore -->

- 使用 rsync 迁移数据

```shell
$ yum -y install rsync
$ rsync -avz /var/lib/docker /home/docker
```

迁移完成后重启 Docker，检查存储路径是否修改、容器和镜像是否正常，验证无误后就可删除原存储目录中的数据。
```shell
$ docker info | grep Dir
$ docker ps -a
$ docker images
$ rm -rf /var/lib/docker/*
```

## 7.卸载 Docker
- CentOS

```shell
$ sudo yum -y remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```
- Ubuntu

```shell
$ sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras -y
```

要删除所有镜像、容器和卷，执行以下命令：
```shell
$ sudo rm -rf /var/lib/docker
$ sudo rm -rf /var/lib/containerd
```