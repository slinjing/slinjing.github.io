---
title: Node_exporter 部署
date: 2023-04-11 11:33:00 +0800
categories: [Prometheus]
tags: [Prometheus]
---


安装包下载地址 [https://prometheus.io/download/](https://prometheus.io/download/)，下载后上传到服务器。

## 1.解压压缩包
```shell
tar -xvf node_exporter-1.5.0.linux-amd64.tar.gz
```

## 2.将 node_exporter 二进制文件复制到 /usr/local/bin 路径下
```shell
cp node_exporter  /usr/local/bin/
```


## 3.创建 systemd service 文件 `/etc/systemd/system/node_exporter.service` 
内容如下：
```shell
[Unit]
Description=node_exporter
After=network.target 
[Service]
ExecStart=/usr/local/bin/node_exporter\
          --web.listen-address=:9100\
          --collector.systemd\
          --collector.systemd.unit-whitelist=(sshd|nginx).service\
          --collector.processes\
          --collector.tcpstat
[Install]
WantedBy=multi-user.target
```

## 4.重载系统 systemd 配置 
```shell
systemctl daemon-reload
```

## 5.启动服务并且设置服务自启
```shell
systemctl start node_exporter
systemctl enable node_exporter
```



## 6.测试接口
```shell
curl -s {{节点IP}}:9100/metrics
```



