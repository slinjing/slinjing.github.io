---
title: Cloudpods 快速部署
date: 2024-06-27 00:34:00 +0800
categories: [Cloudpods]
tags: [Cloudpods]
---

Cloudpods 是一个开源的云原生多云和混合云管理平台‌，不仅可以管理企业内部的物理服务器和虚拟化资源，还能管理来自多个云提供商的云资源。它隐藏了异构基础设施资源的数据模型和 API 的差异，对外暴露了一套统一的 API，使得用户可以方便地与这些多云资源进行程序化交互，大大降低了访问多云的复杂度，提升了管理多云的效率‌。


## 1.配置要求
最低配置要求 CPU 4核，内存 8GiB，存储 100GiB，确保系统没有安装 kubernetes、docker 等容器管理工具，否则部署工具搭建 kubernetes 集群时，会出现冲突导致安装异常。操作系统支持版本参考[官网文档](https://docs-os.yunion.cn/zh/docs/quickstart/allinone-virt/)。

## 2.挂载磁盘
由于虚拟机和服务使用的存储路径都在`/opt`目录下，所以建议单独给`/opt`目录设置挂载点，磁盘分区挂载命令如下：

```shell
fdisk /dev/sdb
mkfs -t ext4 /dev/sdb
mkdir /opt
mount -t ext4 /dev/sdb  /opt
```

## 3.安装 ansible 和 git
```shell
apt install -y git python3-pip
python3 -m pip install --upgrade pip setuptools wheel
python3 -m pip install 'ansible<=9.0.0'
```

## 4.下载 ocboot 工具
```shell
git clone -b release/3.10 https://github.com/yunionio/ocboot && cd ./ocboot
```

## 5.安装 Cloudpods
`host_ip`为部署节点的 IP 地址，如果不指定则选择默认路由出去的那张网卡部署服务。
```shell
./run.py virt <host_ip> 

# 如果安装包下载过慢可以使用以下命令：
./run.py -m https://mirrors.aliyun.com/pypi/simple/ virt <host_ip> 
```

```shell
....
# 部署完成后会有如下输出，表示运行成功
# 浏览器打开 https://10.168.26.216 ，该 ip 为之前设置 <host_ip>
# 使用 admin/admin@123 用户密码登录就能访问前端界面
Initialized successfully!
Web page: https://192.168.200.88
User: admin
Password: admin@123
```

