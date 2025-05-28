---
title: Prometheus 排错
date: 2023-04-11 11:33:00 +0800
categories: [Prometheus]
tags: [Prometheus]
---

## 1.node_exporter_metrics 无 node_processes_threads_state 参数
`processes Disabled by default` 默认禁用，在 node_exporter 启动时加参数：`--collector.processes`。
