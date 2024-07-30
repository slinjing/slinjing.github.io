---
title: Ansible playbook # 必选-文章标题
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
Ansible只需在管理端部署，并且部署方式有多种，这里使用yum元的方式，命令如下：
```shell
$ yum -y install ansible
```

## 主机清单
Ansible根据事先定义好的主机清单，对目标主机进行远程操作，默认的主机清单文件为`/etc/ansible/hosts`。定义时组名需使用[]，主机可以使用域名、IP或别名进行标识，并且可以自定义SSH端口、用户名、密码等，示例如下：
```shell
[web]
192.168.1.10:1022
192.168.1.23 ansible_ssh_pass=123456Aa # 连接目标主机默认用户密码
[db]
192.168.1.11
192.168.1.12 ansible_user=ubuntu  # 连接目标主机默认用户
mysql ansible_ssh_host=192.168.1.50 ansible_ssh_port=1022 # 连接目标主机的地址和端口
```
组成员主机名称支持正则描述，示例如下:
```shell
[web]
www[01:50].test.com
[db]
db-[a:g].test.com
```

## 主机清单变量

### 主机变量
主机可以指定变量，以便Playbooks配置使用，比如定义主机hosts1及hosts2上Apache参数http_port及maxRequestsPerChild，目的是让两台主机产生Apache配置文件httpd.conf差异化，定义格式如下：
```shell
[atlanta]
host1 http_port=80 maxRequestsPerChild=808
host2 http_port=303 maxRequestsPerChild=909
```

### 组变量
组变量的作用域是覆盖组所有成员，通过定义一个新块，块名由组名+“:vars”组成，定义格式如下：
```shell
[atlanta]
host1
host2
[atlanta:vars]
ntp_server=ntp.atlanta.example.com
proxy=proxy.atlanta.example.com
```
同时Ansible支持组嵌套组，通过定义一个新块，块名由组名+“:children”组成，格式如下：
```shell
[atlanta]
host1
host2
[raleigh]
host2
host3
[southeast:children]
atlanta
raleigh
[southeast:vars]
some_server=foo.southeast.example.com
halon_system_timeout=30
self_destruct_countdown=60
escape_pods=2
[usa:children]
southeast
northeast
southwest
southeast
```

### 分离主机与组变量

为了更好规范定义的主机与组变量，Ansible支持将/etc/ansible/hosts定义的主机名与组变量单独剥离出来存放到指定的文件中，将采用YAML格式存放，存放位置规定：“/etc/ansible/group_vars/+组名”和“/etc/ansible/host_vars/+主机名”分别存放指定组名或主机名定义的变量，例如：
```shell
/etc/ansible/group_vars/dbservers
/etc/ansible/group_vars/webservers
/etc/ansible/host_vars/foosball
```

在playbook执行时，可以为主机或组定义变量，比如指定远程登录用户。
```yaml
- hosts：web
  vars：
    worker_processes： 4
    num_cpus： 4
    max_open_file： 65506
    root： /data
  remote_user： root
```
web组定义的变量，变量的作用域只限于web组下的主机。通过vars参数定义了4个变量，其中remote_user为指定远程操作的用户名，默认为root账号，支持sudo方式运行，通过添加sudo：yes即可。

## 任务列表
playbook将按定义的配置文件自上而下的顺序执行，

```yaml
- hosts: web
  remote_user: root
  gather_facts: no
  tasks:
    - name: install nginx
      yum: name=nginx,state=latest
```
galaxy.ansible.com