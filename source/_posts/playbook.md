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
Ansible Playbook提供了一个可重复、可重用、简单的配置管理和多机部署系统，非常适合部署复杂的应用程序。

## 定义主机清单
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