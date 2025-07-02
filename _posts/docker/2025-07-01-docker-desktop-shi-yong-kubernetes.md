---
title: 在 Docker Desktop 使用 Kubernetes
date: 2025-07-01 11:33:00 +0800
categories: [Docker]
tags: [Docker]
---

使用 Docker Desktop 可以方便的启用 Kubernetes，但是国内获取不到 k8s.gcr.io 镜像，可以使用开源项目 [AliyunContainerService/k8s-for-docker-desktop](https://github.com/yeasy/docker_practice) 来获取所需的镜像。

## 1.获取 Kubernetes 镜像
将 k8s-for-docker-desktop 仓库克隆到本地后，切换到 k8s-for-docker-desktop 目录下，Windows 用户使用 PowerShell 执行以下命令：
```shell
.\load_images.ps1
```

> 如果因为安全策略无法执行 PowerShell 脚本，请在 “以管理员身份运行” 的 PowerShell 中执行 Set-ExecutionPolicy RemoteSigned 命令。
如果需要，可以通过修改 images.properties 文件自行加载你自己需要的镜像版本。
{: .prompt-tip }

Mac 上执行如下脚本：
```shell
./load_images.sh
```

## 2.启用 Kubernetes
在 Docker Desktop 设置页面，点击 Kubernetes，选择 Enable Kubernetes，稍等片刻，看到下方 Cluster 中 docker-desktop 状态变为 running，Kubernetes 启动成功。
![Desktop View](/img/Snipaste_2025-07-02_09-57-15.png){: width="972" height="589" }




## 3.测试
```shell
PS C:\Users\Administrator> kubectl version
Client Version: v1.32.2
Kustomize Version: v5.5.0
Server Version: v1.32.2
```
如果正常输出信息，则证明 Kubernetes 成功启动。