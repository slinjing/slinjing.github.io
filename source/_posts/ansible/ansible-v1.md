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
- 部署简单，只需要在主控端部署，被控端无需任何操作；
- 可扩展性和灵活性强，支持各种操作系统、云平台和网络设备的模块化设计，轻松快速地扩展自动化系统；
- 幂等性，一个任务执行1次和执行n次的效果一样，不会因为重复执行带来意外；
- 使用SSH协议对设备进行管理。

![Ansible架构](img/ansible-jiagou.png)
用户通过Ansible编排引擎操作公共/私有云或CMDB（配置管理数据库）中的主机，其中Ansible编排引擎由Inventory（主机与组规则）、API、Modules（模块）、Plugins（插件）组成。

## Ansible安装
Ansible只需在管理端部署环境即可，建议使用yum源方式来实现部署。
```shell
$ yum install ansible -y
```

安装完成后进行配置与测试，修改主机与组配置，文件位置/etc/ansible/hosts，添加两台主机并分别定义到web组和db组，如下：
```yaml
[web]
11.0.1.60
[db]
192.168.32.158
```
为了避免执行Ansible命令时需要输入密码，推荐使用ssh-keygen与ssh-copy-id来实现快速证书的生成及公钥下发
```shell
$ ssh-keygen
$ ssh-copy-id root@192.168.32.158
$ ssh-copy-id root@11.0.1.60
```

通过ping模块测试主机的连通性，返回pong表示连接成功
```shell
$ ansible all -m ping
192.168.32.158 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
11.0.1.60 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

## 定义主机规则
Ansible通过Inventory定义主机与组规则，对匹配的目标主机进行远程操作，配置规则文件默认是/etc/ansible/hosts。

## Ansible相关链接
官网：[https://www.ansible.com/](https://www.ansible.com/)
文档：[https://docs.ansible.com](https://docs.ansible.com)
角色：[https://galaxy.ansible.com/](https://galaxy.ansible.com/)