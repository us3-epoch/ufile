# 工具简介

## 概述

US3VMDS（全称：US3 Virtual Metadata Service）工具是为了解决Hadoop访问UCloud对象存储US3索引速度较慢的问题而引入的一个服务组件。因为Hadoop对分布式文件系统的访问是基于目录树的结构，而对象存储的索引只是简单的KV结构，所以直接通过US3来模拟目录树的移动目录，删除目录等操作时时延高。US3VMDS在内存中基于US3的存储空间索引构建目录树结构，这样客户端可以通过单次交互来实现复杂的逻辑，并可以利用全内存的索引加速如Hadoop对分布式文件系统的getFileStatus，listStatus等操作。

## 原理说明

US3VMDS实现了us3适配器用到的Head、PUT、ListObjects、Delete、Rename、Copy几个API来处理索引请求，并在内存中构建了目录树，其位置类似HDFS实现的NameNode。US3 适配器IO请求则直接发给US3，只是Write IO成功后会通知US3VMDS(时延在150us左右，影响可以忽略不计)。整个架构如下图所示。

![](/images/hadoop_mds_model.png)

大于等于v0.1.0版本的US3VMDS同样支持高可用集群部署，集群中同时存在一个主节点和多个备节点。主节点会如上面描述的那样正常提供缓存服务，备节点则仅会处理索引请求，但是不会将结果在内存中缓存。集群中的各个节点以及US3Hadoop适配器会监听zookeeper中的US3VMDS节点信息变动，一旦发现主节点离线(主节点崩溃，网络不通等问题)，则将一个备用节点晋升为主节点来提供缓存服务。架构如下图:

![](http://us3-release.cn-bj.ufileos.com/us3-bigdata/us3-vmds/static/images/vmds.jpg)

该架构适合加速``加速备份场景``和``中小规模计算分析场景``。

## 运行环境

- Linux
- Mac

