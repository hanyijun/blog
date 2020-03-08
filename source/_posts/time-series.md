---
title: 什么是时序型数据库
date: 2019-03-07 12:34:12
---

## 名词解释
**时序型数据库**：英文名TSDB ：Time Series Database，⽤于保存时间序列（按时间顺序变化）的数据库。

**时间序列**：时间序列是一组按照时间发生先后顺序进行排列的数据点序列。比如每小时气温的变化。时序很容易在图表上进行展示。

![chart](/images/time-series/chart.jpeg)

**度量（metric）**：数据指标的类别，如内存占用，请求量等。 

**域（field）**：数值的含义，比如上图中纵坐标表示温度。可以有多个域，比如描述风的时候，有风力和风向两个域。

**数值（value）**：度量对应的数值，如上图中的12，表示12摄氏度。

**标签（tag）**：⼀个标签是⼀个key-value对，⽤于提供额外的信息。比如以下表示请求总量的指标拥有三个tag。path表示请求路径，method表示请求方法，instance表示机器ip。

requests_total{path="/status", method="GET", instance=”10.0.0.1:80”}

相同度量（metric）的不同标签可以展示在同一张图表上，形成多条数据线。比如上海和杭州的温度变化对比。



## 常用时序型数据库

1. [OpenTSDB](https://github.com/OpenTSDB/opentsdb)：一个分布式的时序型数据库，底层依赖于HBase。

2. [Prometheus](https://github.com/prometheus/prometheus)：一个十分流行的时序型数据库，和K8S生态结合的很好。通过[kube-prometheus](https://github.com/coreos/kube-prometheus)，提供一站式的K8S监控解决方案。也可以通过[thanos](https://github.com/thanos-io/thanos)，让prometheus支持分布式存储。

3. [InfluxDB](https://github.com/influxdata/influxdb)：一个商业化的时序型数据库，分布式版本需要收费。

   