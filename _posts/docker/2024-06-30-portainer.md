---
title: Docker 可视化工具 Portainer
date: 2024-06-30 11:33:00 +0800
categories: [Docker]
tags: [Portainer]
---

Portainer 是一款轻量级的应用，它提供了图形化界面，用于方便地管理 Docker 环境，包括单机环境和集群环境。
官网：<https://www.portainer.io/>

## 1.快速开始
将以下内容保存为`docker-compose.yml`，再执行`docker-compose up -d`命令便可快速启动 Portainer。
```yaml
version: '3.2'
services:
  portainer:
    image: registry.cn-chengdu.aliyuncs.com/shulinjing/portainer-ce:latest
    container_name: portainer
    restart: always
    ports: 
      - 8000:8000
      - 9000:9000
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data

networks:
  portainer_net:

volumes:
  portainer_data:
```

## 2.登陆

第一次登录需创建用户密码，访问地址：http://hostip:9000，设置完成登录后，选择 local 选项卡查看本地 Docker 详细信息展示。
