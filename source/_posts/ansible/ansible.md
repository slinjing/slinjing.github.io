---
title: Ansible # 必选-文章标题
date: 2024-05-07 00:00:00    # 必选-文章创建日期
updated:           # 可选-文章更新日期
tags:              # 可选-文章标          
  - Ansible
categories: Ansible  # 可选-文章分类
keywords:       # 可选-文章关键字
description:    # 可选-文章描述
top_img:        # 可选-文章顶部图片
comments:       # 可选-显示文章评论模块(默认 true)
cover:  '/img/ansible.png' # 可选-文章缩略图(如果没有设置top_img,文章页顶部将显示缩略图，可设为false/图片地址/留空)
---

## 概述
Ansible是一款自动化运维工具，基于Python开发，集合了众多运维工具的优点，实现了批量系统配置、批量程序部署、批量运行命令等功能。它有以下优点：
- 部署简单，只需要在主控端部署，被控端无需任何操作。
- 可扩展性和灵活性强，支持各种操作系统、云平台和网络设备的模块化设计，轻松快速地扩展自动化系统。
- 幂等性和可预测性，playbook运行时，如果目标主机处于正确状态，则Ansible不会做任何更改。
- 使用SSH协议对设备进行管理。

## Ansible安装
Ansible安装建议使用yum命令，更多安装方式参考[安装指南。](https://docs.ansible.com/ansible/latest/index.html)
```shell
$ yum -y install ansible
```



## 配置主机清单
Ansible通过用户定义的主机与组，对匹配的被控主机进行远程操作，默认的主机清单配置文件是`/etc/ansible/hosts`，配置示例如下：
```shell
[test]
jumpserver ansible_ssh_host=192.168.1.50  # 连接目标主机的地址
192.168.1.24 ansible_ssh_port=1022  # 连接目标主机SSH端口，端口22无需指定
192.168.1.23 ansible_user=ubuntu  # 连接目标主机默认用户
192.168.1.23 ansible_ssh_pass=123456Aa # 连接目标主机默认用户密码

```

>更多参数：
ansible_connection：目标主机连接类型，可以是local、ssh或
paramiko。
ansible_ssh_private_key_file：连接目标主机的ssh私钥。
ansible_*_interpreter：指定采用非Python的其他脚本语言，如
Ruby、Perl或其他类似ansible_python_interpreter解释器。


### 主机变量
```shell
[atlanta]
host1 http_port=80 maxRequestsPerChild=808
host2 http_port=303 maxRequestsPerChild=909
```

### 组变量
```shell
[atlanta]
host1
host2
[atlanta：vars]
ntp_server=ntp.atlanta.example.com
proxy=proxy.atlanta.example.com
```


## 模块


## facts变量
```shell
ansible_hostname  # 主机名
ansible_memtotal_mb # 内存总大小
ansible_distribution # 系统版本
ansible_processor_vcpus # cpu数
```