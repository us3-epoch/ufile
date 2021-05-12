# 工具简介

## 概述

us3fs是一个在Linux系统环境中，将us3的存储空间（bucket）挂载到本地挂载点的工具，挂载成功后，您可以像操作本地文件一样操作存储空间（bucket）中的文件。

<video id="video" length=1000 width=800 controls="" preload="none" poster="https://static.ucloud.cn/945f2902d9d0d5451607db50629b8dab.png">
      <source id="mp4" src="http://caozuozhinan.cn-bj.ufileos.com/录屏3 us3 fs.mp4">
      </video>

## 版本和运行环境

### 软件版本

当前版本：v1.5.2

### 运行环境

- Linux：
  - CentOS 7.0 及以上 (可通过`cat /etc/redhat-release`查看)
  - Ubuntu 16.04 及以上 (可通过`cat /etc/issue`查看)

## 主要功能

* 支持POSIX文件系统的大部分功能，如读，顺序写；权限；uid/gid。
* 使用us3的分片上传功能上传大文件。
* 支持etag和MD5校验，保证数据一致性。

## 使用限制

* 不支持读取归档类型的文件
* 不支持随机写/追加写
* rename非原子操作
* 不支持硬/软链接
* 多个客户端挂载同一个us3 bucket时，需要用户自行维护数据一致性。
