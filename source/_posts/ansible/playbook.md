---
title: Playbook # 必选-文章标题
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
playbook是Ansible实现批量自动化最重要的手段。在其中可以使用变量、引用、循环等功能，功能比较强大。一个playbook就是一组play组成的列表，每个play必须包含host和task，task实际是调用ansible的模块去完成具体的任务。tasks下定义一系列的task任务列表，依次执行，如果执行某任务失败了，后续的任务不会执行。


## host与vars
在playbook执行时，可以为主机或组定义变量，比如指定远程登录用户，以及为web组定义变量。
```yaml
- hosts: web
  vars:
    worker_processes: 4
    num_cpus: 4
    max_open_file: 65506
    root: '/data'
  remote_user: root
```

## task
在定义的任务列表中，playbook将按配置文件的顺序执行，定义的主机都将得到相同的任务，但执行的返回结果不一定保持一致。为了增强可读性，可以在每个task定义一个name标签。
```yaml
- hosts: web
  remote_user: root
  tasks:
    - name: install httpd
      yum: name=httpd state=present
    - name: start httpd
      service: name=httpd state=started enabled=yes
```

## handlers和notify
Handlers实际上就是一个触发器，当目标主机配置文件发生变化后，通知Handlers触发后续的动作，比如重启httpd服务，Handlers中定义的动作在没有触发时是不会执行的，触发后也只会运行一次。触发是通过
Handlers定义的name标签来识别的，比如下面notify中的“restart httpd”与handlers中的“name：restart httpd”保持一致。
```yaml
- hosts: web
  remote_user: root
  tasks:
    - name: install httpd
      yum: name=httpd state=present
    - name: install configure file
      copy: src=/root/httpd.conf dest=/etc/httpd/conf/
      notify: restart httpd
    - name: start httpd
      service: name=httpd state=started enabled=yes
  handlers:
    - name: restart httpd
      service: name=httpd state=restarted
```


## 文件复用
当多个playbook涉及复用的任务列表时，可以将复用的内容剥离出来写到独立的文件当中，最后在需要的地方include进来即可，示例：tasks/test.yml
```yaml
---
- name: cat 
  command: cat /root/doc.sh
- name: ls
  command: ls 
```
然后就可以在使用的playbook中include进来，如：
```yaml
- hosts: web
  tasks:
  - include: tasks/test.yml
```


## 执行playbook
执行playbook，可以通过`ansible-playbook`命令实现，如启用10个并行进程数执行playbook：
```shell
$ ansible-playbook a.yml -f 10
```

## Facts
Facts可以获取远程主机的系统信息，包括主机名、IP地址、操作系统、分区信
息、硬件信息等，可以配合playbook实现更加个性化、灵活的功能需求，比如在模板中引用Facts的主机名信息。
```shell
$ ansible 11.0.1.60 -m setup
```
运行以上命令可以获取Facts信息，在模板文件中按下面的方式引用：
```yaml
{{ ansible_devices.sda.model }}
{{ ansible_hostname }}
```

## Jinja2


## 角色
角色是Ansible定制好的一种标准规范，以不同级别目录层次及文件对角色、变量、任务、处理程序等进行拆分，为后续功能扩展、可维护性打下基础。一个角色目录结构的示例如下：
```shell
roles/
  webservers/  # 角色名称
    files/   # 存放由copy或script模块等调用的文件
    templates/  # 模板文件的目录
    tasks/   # 定义task,role的基本元素，至少应该包含一个名为main.yml的文件；其它的文件需要在此文件中通过include进行包含
    handlers/  # 至少应该包含一个名为main.yml的文件；其它的文件需要在此文件中通过include进行包含
    vars/   # 定义变量，至少应该包含一个名为main.yml的文件；其它的文件需要在此文件中通过include进行包含
    meta/  # 定义当前角色的特殊设定及其依赖关系,至少应该包含一个名为main.yml的文件
```

### 创建playbook目录
playbook目录包括变量定义目录group_vars、主机组定义文件hosts、全局配置文件site.yml、角色功能等，此处创建为[playbooks/nginx]。

### 定义主机组
[nginx/hosts]
```yaml
[web]
11.0.1.60
```

### 定义主机或组变量
[nginx/group_vars/all]
```yaml
---
ntpserver: ntp.sjtu.edu.cn
```

[nginx/group_vars/webservers]
```yaml
---
worker_processes： 4
num_cpus： 4
max_open_file： 65536
root： /data
```

### 全局配置文件
[nginx/site.yml]
```yaml
---
- name: apply common configuration to all nodes
  hosts: all
  roles:
    - common
- name: configure and deploy the webservers and application code
  hosts: web
  roles:
    - web
```
全局配置文件site.yml引用了两个角色，一个为公共类的common，另一个为web类，分别对应nginx/common、nginx/web目录。以此类推，可以引用更多的角色，如db、nosql、hadoop等，前提是我们先要进行定义，通常情况下一个角色对应着一个特定功能服务。通过hosts参数来绑定角色对应的主机或组。

### 定义common角色
角色common定义了handlers、tasks、templates、vars 4个功能类，分别存放处理程序、任务列表、模板、变量的配置文件main.yml，需要注意的是，vars/main.yml中定义的变量优先级高于/nginx/group_vars/all，可以从ansible-playbook的执行结果中得到验证。各功能块配置文件定义如下：
[handlers/main.yml]
```yaml
- name: restart ntp
  service: name=ntpd state=restarted
```

[tasks/main.yml]
```yaml
- name: Install ntp
  yum: name=ntp state=present
- name: Configure ntp file
  template: src=ntp.conf.j2 dest=/etc/ntp.conf
  notify: restart ntp
- name: Start the ntp service
  service: name=ntpd state=started enabled=true
- name: test to see if selinux is running
  command: getenforce
  register: sestatus
  changed_when: false
```
其中template：src=ntp.conf.j2引用模板时无需写路径，默认在上级的templates目录中查找。

[templates/ntp.conf.j2]
```yaml
driftfile /var/lib/ntp/drift
restrict 127.0.0.1
restrict -6 ：：1
server {{ ntpserver }}
includefile /etc/ntp/crypto/pw
keys /etc/ntp/keys
```
此处{{ ntpserver }}将引用vars/main.yml定义的ntpserver变量。

[vars/main.yml]
```yaml
---
# Variables listed here are applicable to all host groups
ntpserver: 210.72.145.44
```

### 定义web角色
角色web定义了handlers、tasks、templates三个功能类，基本上是9.6节中的nginx管理playbook对应定义功能段打散后的内容。具体功能块配置文件定义如下：
[handlers/main.yml]
```yaml
- name: restart nginx
  service: name=nginx state=restarted
```

[tasks/main.yml]
```yaml
- name: ensure nginx is at the latest version
  yum: pkg=nginx state=latest
- name: write the nginx config file
  template: src=nginx2.conf dest=/etc/nginx/nginx.conf
  notify:
  - restart nginx
- name: ensure nginx is running
  service: name=nginx state=started
```

[templates/nginx2.conf]
```yaml
user nginx；
worker_processes {{ worker_processes }}；
{% if num_cpus == 2 %}
worker_cpu_affinity 01 10；
{% elif num_cpus == 4 %}
worker_cpu_affinity 1000 0100 0010 0001；
{% elif num_cpus >= 8 %}
worker_cpu_affinity 00000001 00000010 00000100 00001000 00010000 00100000
01000000 10000000；
{% else %}
worker_cpu_affinity 1000 0100 0010 0001；
{% endif %}
worker_rlimit_nofile {{ max_open_file }}；
```