# 工具简介

## 概述

US3Hadoop适配器是为了解决Hadoop访问UCloud对象存储US3的问题，实现了标准的Hadoop文件系统，支持Hive、Spark、Flink等大数据计算框架可以像访问HDFS文件系统一样，来读写存储在US3上的数据。

## 原理说明

适配器是实现了Hadoop提供的存储访问接口FileSystem的适配器组件，类似HDFS实现的DistributedFileSystem和基于AWS S3协议实现的S3AFileSystem。适配器直接把IO，索引请求发给US3，架构如下图所示。

![](/images/hadoop_no_mds.png)

该架构适合**备份场景**和**小规模计算分析场景**。

## 版本和运行环境

### 软件版本

当前版本：1.0.1

### 运行环境

  - hadoop-2.6.0
  - hadoop-2.8.3
  - hadoop-2.8.5
  - hadoop-3.1.1