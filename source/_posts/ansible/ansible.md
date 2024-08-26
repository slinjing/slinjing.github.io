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

官网：[https://www.ansible.com/](https://www.ansible.com/)
官方文档：[https://docs.ansible.com/ansible/latest/getting_started/introduction.html](https://docs.ansible.com/ansible/latest/getting_started/introduction.html)

## Ansible架构
![img](/img/ansible-1.png)

ansible是基于模块工作的，本身没有批量部署的能力。真正具有批量部署的是ansible所运行的模块，ansible只是提供一种框架。
主要包括：
    (1)、连接插件connection plugins：负责和被监控端实现通信；
    (2)、host inventory：指定操作的主机，是一个配置文件里面定义监控的主机；
    (3)、各种模块核心模块、command模块、自定义模块；
    (4)、借助于插件完成记录日志邮件等功能；
    (5)、playbook：剧本执行多个任务时，非必需可以让节点一次性运行多个任务。

## Ansible主要组成部分
>ANSIBLE PLAYBOOKS：任务剧本（任务集），编排定义Ansible任务集的配置文件，由Ansible顺序依次执行，通常是JSON格式的YML文件
INVENTORY：Ansible管理主机的清单  /etc/anaible/hosts
MODULES：Ansible执行命令的功能模块，多数为内置核心模块，也可自定义
PLUGINS：模块功能的补充，如连接类型插件、循环插件、变量插件、过滤插件等，该功能不常用
API：供第三方程序调用的应用程序编程接口 
ANSIBLE：组合INVENTORY、API、MODULES、PLUGINS的绿框，可以理解为是ansible命令工具，其为核心执行工具

## Ansible安装
Ansible可以使用多种安装方式，其中最方便的是通过EPEL源安装。
### yum安装
```shell
$ yum -y install ansible
```
### 编译安装
```shell
$ yum -y install python-jinja2 PyYAML python-paramiko python-babel
$ python-crypto
$ tar xf ansible-1.5.4.tar.gz
$ cd ansible-1.5.4
$ python setup.py build
$ python setup.py install
$ mkdir /etc/ansible
$ cp -r examples/* /etc/ansible
```
### pip安装
```shell
$  yum install python-pip python-devel
$  yum install gcc glibc-devel zibl-devel rpm-bulid openssl-devel
$  pip install --upgrade pip
$  pip install ansible --upgrade
```
### 验证
```shell
$ ansible --version
```

## 主机清单
Ansible根据事先定义好的主机清单，对目标主机进行远程操作，默认的主机清单文件为`/etc/ansible/hosts`。定义时组名需使用[]，主机可以使用域名、IP或别名进行标识，并且可以自定义SSH端口、用户名、密码等，示例如下：

```shell
[web]
192.168.32.158
[db]
11.0.1.60
```
除了以上的方式外还支持正则写法来定义主机，如下：
```shell
[dbs]
db-[a-f].example.com
[wes]
www[1:100].example.com
```


jumper为定义的一个别名，ansible_ssh_port为主机SSH服务端口，ansible_ssh_host为目标主机，更多保留主机变量如下：
```shell
ansible_ssh_host # 连接目标主机的地址
ansible_ssh_port # 连接目标主机SSH端口，端口22无需指定
ansible_ssh_user # 连接目标主机默认用户
ansible_ssh_pass # 连接目标主机默认用户密码
ansible_connection # 目标主机连接类型，可以是local、ssh或paramiko
ansible_ssh_private_key_file # 连接目标主机的ssh私钥
ansible_*_interpreter # 指定采用非Python的其他脚本语言，如Ruby、Perl或其他类似ansible_python_interpreter解释器
```
主机名支持正则写法：
```shell
[dbs]
db-[a-f].example.com
[web]
www[1:100].example.com
```

完成后测试主机是否连通
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
执行状态说明：
绿色：执行成功并且不需要做改变的操作
黄色：执行成功并且对目标主机做变更
红色：执行失败



## ansible常用模块

### Command
Command是默认模块，它可以在远程主机执行命令，可忽略-m选项，用法如下：
```shell
$ ansible all -m command -a 'free -h'
```
此模块不支持 变量 < > | ; & 等,可以用shell模块实现。
更多用法：
```shell
$ ansible all -m command -a 'chdir=/home ls'
# chdir 进入到被管理主机目录，再执行Command命令
$ ansible all -m command -a 'creates=/home mkdir /home'
# creates 如果目录存在,则不执行Command命令
```

### Shell
和command相似，用shell执行命令
```shell
$ ansible all -m shell  -a 'getenforce'
$ ansible all -m shell  -a "sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config"
```

### Script
在远程主机上运行ansible服务器上的脚本，相当于SCP+Shell的组合
```shell
$ ansible all -m script -a hello.sh
```

### Copy
从主控端复制文件到远程主机，参数：
>src : 源文件路径
dest: 指定目标路径
mode: 设置权限
backup: 备份源文件
content: 代替src，指定文件内容,生成目标主机文件
```shell
$ ansible all -m copy -a "src=/root/hello.sh dest=/tmp/test.sh mode=600 backup=yes"
$ ansible all -m copy -a "content='hello world\n' dest=/root/test.txt"
```

### Fetch
从远程主机提取文件至主控端，与copy相反
```shell
# 会生成每个被管理主机命名的目录
$ ansible all -m fetch -a 'src=/root/test.txt dest=/root'

# 对于目录,可以先打包,再提取文件
$ ansible all -m shell -a 'tar -czvf test.tar.gz /root/test'
$ ansible all -m fetch -a 'src=/root/test.tar.gz dest=/root'
```

### File
创建文件、目录，设置文件属性等，参数：
>path: 要管理的文件路径 (强制添加)
    recurse: 递归,文件夹要用递归
    src:  创建硬链接,软链接时,指定源目标,配合'state=link' 'state=hard' 设置软链接,硬链接
    state: 状态
```shell
# 创建文件
$ ansible all -m file -a 'path=/root/app state=touch'
# 创建目录
$ ansible web -m file -a 'path=/data/app state=directory'
# 设置权限
$ ansible all -m file -a 'path=/root/app owner=root mode=755'
# 创建软链接
$ ansible web -m file -a 'src=/root/app dest=/home/app state=link'
# 删除文件/目录
$ ansible all -m file -a 'path=/root/app state=absent'
```


### stat
获取远程文件状态信息
```shell
$ ansible all -m stat -a "path=/etc/sysctl.conf"
```

### get_url
在远程主机下载指定URL到本地
```shell
$ ansible all -m get_url -a "url=http://www.baidu.com dest=/tmp/index.html  mode=0440 force=yes"
```

### yum
软件包管理操作，常见有yum、apt管理方式
```shell
# 查看程序列表
$ ansible all -m yum -a 'list=httpd'
# 安装
$ ansible all -m yum -a 'name=httpd state=present' 
# 卸载
$ ansible all -m yum -a 'name=httpd state=absent'
```

### Service
管理服务
```shell
# 停止服务
$ ansible all -m service -a 'name=httpd state=stopped'
# 启动服务,并设为开机自启
$ ansible all -m service -a 'name=httpd state=started enabled=yes' 
# 重新加载
$ ansible all -m service -a 'name=httpd state=reloaded'  
# 重启服务
$ ansible all -m service -a 'name=httpd state=restarted' 
```

### User
管理用户，参数:
>    home：指定家目录路径
    system：指定系统账号
    group：指定组
    remove：清除账户
    shell：指定shell类型

```shell
# 创建用户
$ ansible all -m user -a 'name=tom uid=88 system=yes home=/app groups=root shell=/sbin/nologin password="123456Aa"'
# 删除用户
$ ansible all -m user -a 'name=tom state=absent remove=yes'
```

### Cron
计划任务，支持时间：minute,hour,day,month,weekday
```shell
# 创建任务
$ ansible all -m cron -a "name='check dirs' hour='5,2' job='ls -alh > /dev/null'"
# 删除任务
$ ansible all -m cron -a 'state=absent name="check dirs"'
# 注销任务
$ ansible all -m cron -a "name='check dirs' hour='5,2' job='ls -alh > /dev/null' disabled=yes"
```

### mount
远程主机分区挂载
```shell
$ ansible all -m mount -a "name=/mnt/data src=/dev/sd0 fstype=ext3 opts=ro state=present"
```

更多模块用法参考[Ansible模块和插件文档。](https://docs.ansible.com/ansible/latest/modules/modules_by_category.html)


## playbook
playbook是Ansible实现批量自动化最重要的手段。在其中可以使用变量、引用、循环等功能，功能比较强大。一个playbook就是一组play组成的列表，每个play必须包含host和task，play主要功能在于将预定义的一组主机，task实际是调用ansible的模块去完成具体的任务。tasks下定义一系列的task任务列表，依次执行，如果执行某任务失败了，后续的任务不会执行。

### host与vars
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

### task
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

### handlers和notify
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

### 执行playbook
执行playbook，可以通过`ansible-playbook`命令实现，如启用10个并行进程数执行playbook：
```shell
$ ansible-playbook a.yml -f 10
```

### 文件复用
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

## 变量
### 主机变量
```shell
[web]
192.168.1.11 port=80 maxRequestsPerChild=808
```

### 组变量
```shell
[web]
192.168.1.11
192.168.1.12
[web:vars]
ntp_server=ntp.atlanta.example.com
proxy=proxy.atlanta.example.com
```

## 拆分主机与组变量
为了更好规范定义的主机与组变量，Ansible支持将/etc/ansible/hosts定义的主机名与组变量单独剥离出来存放到指定的文件中，将采用YAML格式存放，存放位置规定：“/etc/ansible/group_vars/+组名”和“/etc/ansible/host_vars/+主机名”分别存放指定组名或主机名定义的变量，例如：
```shell
/etc/ansible/group_vars/dbservers
/etc/ansible/group_vars/webservers
/etc/ansible/host_vars/foosball
```
定义的db的变量`/etc/ansible/group_vars/dbservers`
```yaml
---
ntp_server: acme.example.org
database_server: storage.example.org
```
group_vars/和host_vars/目录可以保存在playbook目录或inventory目录，如同时存在，inventory目录的优先级高于playbook目录的。





## facts变量
```shell
ansible_hostname  # 主机名
ansible_memtotal_mb # 内存总大小
ansible_distribution # 系统版本
ansible_processor_vcpus # cpu数
```