---
title: Windows_exporter 部署
date: 2023-04-11 11:33:00 +0800
categories: [Prometheus]
tags: [Prometheus]
---

## 1.安装包下载
https://github.com/prometheus-community/windows_exporter/releases

## 2.在注册表和服务数据库中创建windows_exporter服务项
```shell
sc create windows_exporter binpath= "D:\windows_exporter\windows_exporter-0.21.0-386.exe" type= own start= auto displayname= windows_exporter
```
# binpath 后接的是.exe 程序所在的目录及程序名称

## 3.启动注册的 windows_export 服务
输入启动参数：–telemetry.addr=127.0.0.1:9182

## 4.运行windows_exporter服务

## 5.删除服务
```shell
sc delete windows_exporter
```