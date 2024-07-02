---
title: 使用Prometheus实现监控与告警  # 必选-文章标题
date: 2020-02-03 00:00:00    # 必选-文章创建日期
updated:           # 可选-文章更新日期
tags:              # 可选-文章标          
  - Prometheus
  - Alertmanager
  - Grafana
  - Node_exporter
categories: Prometheus  # 可选-文章分类
keywords:       # 可选-文章关键字
description:    # 可选-文章描述
top_img:        # 可选-文章顶部图片
comments:       # 可选-显示文章评论模块(默认 true)
cover:  /img/Prometheus.png # 可选-文章缩略图(如果没有设置top_img,文章页顶部将显示缩略图，可设为false/图片地址/留空)
---

## 概述
随着时代的发展，社会步入互联网时代，在互联网时代中人人都可以享受互联网带来的便利。但是这些互联网服务也可能出现各种问题，也需要有人来维护。但由于故障的不确定性和突发性，这个维护过程往往使人心力交瘁，所以有一套完善的监控以及故障告警系统是非常必要的，它可以有效的帮助我们发现问题，从而去解决问题，本文以Prometheus以及周边生态搭建一套监控告警系统。


## 安装Prometheus

Prometheus是一个开源的系统监控和警报工具，它可以收集监控目标的指标，并根据预设的规则进行告警。并且周边生态非常完善，可以满足日常大部分业务需求。Prometheus的安装方式有好几种，可以参考[官方文档](https://prometheus.io/docs/prometheus/latest/installation/),这里使用二进制文件进行安装，可以去[github仓库](https://github.com/prometheus/prometheus/releases)或者点击二进制文件[下载地址](https://prometheus.io/download/)根据自己的系统版本进行下载，建议选择LTS版本。linux_x86系统可以使用以下命令：

```shell
$ wget https://github.com/prometheus/prometheus/releases/download/v2.45.1/prometheus-2.45.1.linux-amd64.tar.gz
$ tar -zxvf prometheus-2.45.1.linux-amd64.tar.gz
$ mv prometheus-2.45.1.linux-amd64 /home/prometheus
```

下载并解压之后，接下来在`/usr/lib/systemd/system/`或者`/etc/systemd/system/`目录下创建一个`prometheus.service`文件，内容如下：

```shell
[Unit]
Description=Prometheus Server
Documentation=https://prometheus.io/docs/introduction/overview/
After=network-online.target
[Service]
User=root
Restart=on-failure
ExecStart=/home/prometheus/prometheus \
--config.file=/home/prometheus/prometheus.yml \
--storage.tsdb.path=/home/prometheus/data
[Install]
WantedBy=multi-user.target
```

完成后载入配置并启动Prometheus服务，验证本地9090端口或者访问http//host_ip:9090无误即可。

```shell
$ systemctl daemon-reload
$ systemctl enable prometheus --now

$ netstat -plntu |grep 9090
tcp6       0      0 :::9090                 :::*                    LISTEN      25105/prometheus
```



## 安装alertmanager
Alertmanager的作用是负责报警，安装步骤同Prometheus一样，[github仓库](https://github.com/prometheus/alertmanager/releases)或者点击二进制文件[下载地址](https://prometheus.io/download/)均可下载，linux_x86系统可以使用以下命令：

```shell
$ wget https://github.com/prometheus/alertmanager/releases/download/v0.24.0/alertmanager-0.24.0.linux-amd64.tar.gz
$ tar -zxvf alertmanager-0.24.0.linux-amd64.tar.gz
$ alertmanager-0.24.0.linux-amd64/ /home/alertmanager
```

下载解压后创建alertmanager.service文件，内容如下：

```shell
[Unit]
Description=alertmanager
Documentation=https://prometheus.io/
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
User=root
Restart=on-failure
ExecStart=/home/alertmanager/alertmanager --storage.path=/home/alertmanager/data/ \
--config.file=/home/alertmanager/alertmanager.yml

[Install]
WantedBy=default.target
```

载入配置并启动Alertmanager服务，通过端口9093或访问http://host_ip:9093验证。

```bash
$ systemctl daemon-reload
$ systemctl enable alertmanager --now

$ netstat -plntu |grep 9093
tcp6       0      0 :::9093                 :::*                    LISTEN      26103/alertmanager
udp6       0      0 :::9093                 :::*                                26103/alertmanager
```

## 安装Grafana
Grafana是一款可视化和分析软件，通过连接Prometheus数据源，可以将监控数据可视化展示。不同操作系统安装可以参考[下载 Grafana](https://grafana.com/grafana/download),注意要将Edition选项切换到OSS也就是开源版本。本文通过RPM包进行安装，命令如下：

```shell
$ wget https://dl.grafana.com/oss/release/grafana-9.1.5-1.x86_64.rpm
$ yum -y install grafana-9.1.5-1.x86_64.rpm
```
安装完成后即可启动Grafana服务，默认账户密码：admin/admin，访问地址为：http//host_ip:3000。

```shell
$ systemctl enable grafana-server --now
```


## 安装Node_exporter
接下来安装Node_exporter，它只需要安装在被监控主机上，可以前往[github仓库](https://github.com/prometheus/node_exporter/releases)或者点击二进制文件[下载地址](https://prometheus.io/download/)进行下载，linux_x86系统可以使用以下命令：

```shell
$ wget https://github.com/prometheus/node_exporter/releases/download/v1.5.0/node_exporter-1.5.0.linux-amd64.tar.gz
$ tar -zxvf node_exporter-1.5.0.linux-amd64.tar.gz
$ mv node_exporter-1.5.0.linux-amd64/ /home/node_exporter
```


下载解压后创建node_exporter.service文件，内容如下：

```shell
[Unit]
Description=node_exporter
After=network.target
[Service]
ExecStart=/home/node_exporter/node_exporter\
          --web.listen-address=:9100\
          --collector.systemd\
          --collector.systemd.unit-whitelist=(sshd|nginx).service\
          --collector.processes\
          --collector.tcpstat
[Install]
WantedBy=multi-user.target
```

载入配置并启动Node_exporter服务，通过端口9100或访问http://host_ip:9100验证。

```shell
$ systemctl daemon-reload
$ systemctl enable node_exporter --now

$ netstat -plntu |grep 9100
tcp6       0      0 :::9100                 :::*                    LISTEN      1355/node_exporter
```


## 配置监控目标
以上准备工作都完成后，修改Prometheus服务器配置，加入被监控主机，这样就可以在Prometheus服务器上获取到被监控主机的监控指标，修改`/home/prometheus/prometheus.yml`文件，末尾添加如下内容：

```yaml
  - job_name: "node_exporter"
    static_configs:
      - targets: ["node_exporter_host_ip:9100"]
```

修改完成后为了避免出错，可以通过promtool工具，检测配置文件是否正确，进入promtool工具所在目录执行下面命令，返回SUCCESS则成功：

```shell
$ ./promtool check config prometheus.yml
Checking prometheus.yml
 SUCCESS: prometheus.yml is valid prometheus config file syntax
```
修改好配置文件后可以重启Prometheus服务，或者使用下面命令使配置生效：

```shell
curl -X POST http://localhost:9090/-/reload
```
重启Prometheus服务或者热加载配置后,访问http://host_ip:9090验证一下，点击Status-->Targets能看到刚刚添加的node_exporter主机信息则成功。![img](/img/node_exporter.png)


## 2.Grafana数据展示
### 添加数据源
上面已经成功添加监控主机,接下来在Grafana上展示数据，首先登录Grafana首次登录需修改密码。登录后添加Prometheus数据源,点击左侧导航栏Home下的Plugis然后找到Prometheus，点击installed-->Add new date source,接着在Prometheus server URL中输入自己的Prometheus地址点击Save & test保存。

### 添加Dashboard
数据源添加好后就可以展示数据了,这里需要用到Grafana的Dashboard,点击左侧导航栏Dashboard-->New-->import输入ID：11074点击Load，VictoriaMetrics选择Prometheus再点击保存，回到首页可以看到Dashboard已经显示出刚刚添加的面板，点击即可查看。
![img](/img/node-dashboard.png)

更多面板查看[Grafana Dashboard。](https://grafana.com/grafana/dashboards/)

## 告警配置
在Prometheus中，如果被监控主机的指标到达告警规则预设的阈值时，就会触发告警，告警的动作是由Alertmanager来完成。那么要想实现告警首先需要保证Prometheus能够调用Alertmanager，在Prometheus的配置文件中添加Alertmanager地址，如下：
```yaml
alerting:
  alertmanagers:
    - static_configs:
        - targets:
           - localhost:9093 # Alertmanager地址
```

### 创建告警规则
以上配置完成后还需要创建告警规则，因为Prometheus是根据我们预先设定的规则来进行告警的，这里简单创建一个主机宕机的规则来测试，如果需要设定更多告警规则可以参考[Awesome Prometheus alerts。](https://samber.github.io/awesome-prometheus-alerts/)
我将告警规则放到`/home/prometheus/rule/`目录下，命名为`host-alerts.yaml`，内容如下：
```yaml
groups:
- name: host
  rules:
  - alert: 主机宕机
    expr: up == 0
    for: 1m
    labels:
      severity: critical
    annotations:
      summary: 服务器宕机 (instance {{ $labels.instance }})
      description: "服务器宕机，或者node exporter未启动\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
```
关于告警规则的定义可以参考[官方文档，](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/)完成后重启服务。




### 配置告警接收器
支持多种告警方式，[官方文档](https://prometheus.io/docs/alerting/latest/configuration/)有详细说明，本文以webhook为例。

>企业微信
```yaml
global:
  resolve_timeout: 10m
  wechat_api_url: 'https://qyapi.weixin.qq.com/cgi-bin/'
  wechat_api_secret: '应用的secret'
  wechat_api_corp_id: '企业id'
templates:
- '/etc/alertmanager/config/*.tmpl'
route:
  group_by: ['alertname']
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  routes:
  - receiver: 'wechat'
    continue: true
inhibit_rules:
- source_match:
receivers:
- name: 'wechat'
  wechat_configs:
  - send_resolved: false
    corp_id: '企业id'
    to_user: '@all'
    to_party: ' PartyID1 | PartyID2 '
    message: '{{ template "wechat.default.message" . }}'
    agent_id: '应用的AgentId'
    api_secret: '应用的secret'
```
消息模板如下：
```yaml
{{ define "wechat.default.message" }}
{{- if gt (len .Alerts.Firing) 0 -}}
{{- range $index, $alert := .Alerts -}}
{{- if eq $index 0 -}}
告警类型: {{ $alert.Labels.alertname }}
告警级别: {{ $alert.Labels.severity }}

=====================
{{- end }}
===告警详情===
告警详情: {{ $alert.Annotations.message }}
故障时间: {{ $alert.StartsAt.Format "2006-01-02 15:04:05" }}
===参考信息===
{{ if gt (len $alert.Labels.instance) 0 -}}故障实例ip: {{ $alert.Labels.instance }};{{- end -}}
{{- if gt (len $alert.Labels.namespace) 0 -}}故障实例所在namespace: {{ $alert.Labels.namespace }};{{- end -}}
{{- if gt (len $alert.Labels.node) 0 -}}故障物理机ip: {{ $alert.Labels.node }};{{- end -}}
{{- if gt (len $alert.Labels.pod_name) 0 -}}故障pod名称: {{ $alert.Labels.pod_name }}{{- end }}
=====================
{{- end }}
{{- end }}

{{- if gt (len .Alerts.Resolved) 0 -}}
{{- range $index, $alert := .Alerts -}}
{{- if eq $index 0 -}}
告警类型: {{ $alert.Labels.alertname }}
告警级别: {{ $alert.Labels.severity }}

=====================
{{- end }}
===告警详情===
告警详情: {{ $alert.Annotations.message }}
故障时间: {{ $alert.StartsAt.Format "2006-01-02 15:04:05" }}
恢复时间: {{ $alert.EndsAt.Format "2006-01-02 15:04:05" }}
===参考信息===
{{ if gt (len $alert.Labels.instance) 0 -}}故障实例ip: {{ $alert.Labels.instance }};{{- end -}}
{{- if gt (len $alert.Labels.namespace) 0 -}}故障实例所在namespace: {{ $alert.Labels.namespace }};{{- end -}}
{{- if gt (len $alert.Labels.node) 0 -}}故障物理机ip: {{ $alert.Labels.node }};{{- end -}}
{{- if gt (len $alert.Labels.pod_name) 0 -}}故障pod名称: {{ $alert.Labels.pod_name }};{{- end }}
=====================
{{- end }}
{{- end }}
{{- end }}
```

### 3.5检查配置文件

```bash
$ ./amtool check-config alertmanager.yml
Checking 'alertmanager.yml'  SUCCESS
Found:
 - global config
 - route
 - 0 inhibit rules
 - 1 receivers
 - 1 templates
  SUCCESS
```

### 3.6创建告警模板

```bash
mkdir -p /home/alertmanager/template
vim /home/alertmanager/template/test.tmpl


{{ define "wechat.default.message" }}
{{- if gt (len .Alerts.Firing) 0 -}}
{{- range $index, $alert := .Alerts -}}
{{- if eq $index 0 }}
========= 监控报警 =========
告警状态：{{   .Status }}
告警级别：{{ .Labels.severity }}
告警类型：{{ $alert.Labels.alertname }}
故障主机: {{ $alert.Labels.instance }}
告警主题: {{ $alert.Annotations.summary }}
告警详情: {{ $alert.Annotations.message }}{{ $alert.Annotations.description}};
触发阀值：{{ .Annotations.value }}
故障时间: {{ ($alert.StartsAt.Add 28800e9).Format "2006-01-02 15:04:05" }}
========= = end =  =========
{{- end }}
{{- end }}
{{- end }}
{{- if gt (len .Alerts.Resolved) 0 -}}
{{- range $index, $alert := .Alerts -}}
{{- if eq $index 0 }}
========= 异常恢复 =========
告警类型：{{ .Labels.alertname }}
告警状态：{{   .Status }}
告警主题: {{ $alert.Annotations.summary }}
告警详情: {{ $alert.Annotations.message }}{{ $alert.Annotations.description}};
故障时间: {{ ($alert.StartsAt.Add 28800e9).Format "2006-01-02 15:04:05" }}
恢复时间: {{ ($alert.EndsAt.Add 28800e9).Format "2006-01-02 15:04:05" }}
{{- if gt (len $alert.Labels.instance) 0 }}
实例信息: {{ $alert.Labels.instance }}
{{- end }}
========= = end =  =========
{{- end }}
{{- end }}
{{- end }}
{{- end }}
```

## 4.webhook

```bash
docker run --name webhook-adapter -p 8080:80 -d guyongquan/webhook-adapter --adapter=/app/prometheusalert/wx.js=/wx=xx  #企业微信机器人地址
```

![image-20231220132944613](F:\docs\img\image-202312201329446113.png)