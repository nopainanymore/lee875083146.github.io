---
title: 使用Micrometer进行应用监控
tags: [监控,Micrometer,SpringBoot]
categories:
- 监控
- Micrometer
date: 2019-11-01 14:37:18
entitle: Monintoring-Micrometer
---

本文介绍JVM监控工具Micrometer。

<!--more-->
## 背景

监控对于衡量系统、应用的运行健康，以及问题定位排查、 提前预警等方面都至关重要。性能指标监控通常由两个部分组成：性能指标的收集暴露；监控数据的聚合计算和提供API接口。

在应用中一般使用计数器Counter、计量仪、计时器来记录关键的性能指标。在专用的监控系统中对性能指标进行汇总，并生成相应的图标来进行可视化的分析。

目前有很多种的监控系统，常用的有Prometheus、DataDog等，每个系统都有

## 介绍
Micrometer为Java平台


## 参考资料
<https://micrometer.io/docs>
<https://micrometer.io/docs/registry/prometheus>
<https://www.ibm.com/developerworks/cn/java/j-using-micrometer-to-record-java-metric/index.html>
