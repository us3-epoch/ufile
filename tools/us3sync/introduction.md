# 工具简介

## 概述

US3SYNC 是一款将不同源的数据同步到 US3 的迁移工具。通过将 US3SYNC 部署在本地或者云主机中，可以便捷地从本地或者其他云环境中将数据迁移到 US3 存储空间。US3SYNC工具可以创建两种模式的任务：

* **普通模式：** 普通同步任务可以将源端某个指定前缀或文件夹的文件批量的同步到US3上。

* **fetch模式：** fetch模式的同步任务是通过POST请求（描述需要同步的文件URL和目的位置）将文件同步到指定的位置上。可以指明文件同步成功和失败后的回调接口，US3SYNC会在同步任务完成后，发送相应的通知。

### 普通任务工作原理

![](https://ufile-release.cn-bj.ufileos.com/us3sync/doc/structure.jpg)

图中master节点与worker节点功能：

- master节点：
> 单点部署，负责迁移任务的管理。其主要逻辑是从源端拉取文件列表，然后将需要迁移的文件派发给worker进程迁移。

- worker节点：
> 支持节点扩展，负责迁移文件。其主要逻辑是从源端下载文件，然后将文件上传到目的端。

master节点与worker节点可以部署在同一台机器，也可以部署在多台机器上，用户可以根据需要自行扩展worker节点，下面分别介绍：

- 部署在同一台机器：
> master节点和worker节点通过启动时配置的**内部通信监听地址**进行通信。用户需要确保配置给worker节点的路径是单独的路径，不可与master路径以及其他worker路径重复。

- 部署在不同机器：
> master节点和worker节点通过启动时配置的**内部通信监听地址**进行通信，确保该地址在worker机器上可以访问。用户需要确保配置给worker节点的路径是单独的路径，不可与master路径以及其他worker路径重复。

### Fetch任务工作原理

Fetch类型的任务跟普通任务工作原理大致类似，不同的是同步任务的源端需要**发送POST请求**来指定，而不是根据配置的源端的位置自动拉取。

#### POST请求语法

```HTTP
POST /fetch/ HTTP 1.1
Authorization: Auth
Content-Type:application/json
```

##### 请求头部

Authorization为必选项，content-type, date等都是可选项

例如： Authorization: UCloud  xxxx-xxxx-xxxx-xxxx-xxxx:xxxxxxxxxxxxx
计算方法伪码如下：

```go
method := "POST"
md5 := xxxxxx
contentType := xxxxxx
date := xxxxx
privateKey = xxxxx-xxxx-xxxxx-xxxx
publicKey = xxxx-xxxx-xxxx-xxxx
strToSign = method + "\n" + md5 + "\n" + contentType + "\n" + date + "\n"
signature = HmacSHA1(strToSign, privateKey)
signature = Base64(signature)
Authorization: "UCloud " + publicKey + ":" + signature
```



##### 请求内容

| 名称               | 描述                           | 类型   | 必选 |
| ------------------ | ------------------------------ | ------ | ---- |
| Url                | 源站资源地址,需要url encode    | String | 是   |
| Key                | 桶内文件路径，不需要url encode | String | 是   |
| Bucket             | 桶名                           | String | 是   |
| JobId              | Fetch任务Id                    | String | 是   |
| SuccessCallbackUrl | 回源拉取成功的回调地址         | String | 否   |
| FailureCallbackUrl | 回源拉取失败的回调地址         | String | 否   |

此处的JobId可以在界面中获取

###### 返回内容

| 名称    | 描述         | 类型   |
| ------- | ------------ | ------ |
| RetCode | 请求状态码   | Int    |
| ErrMsg  | 请求信息     | String |
| TaskId  | 任务的唯一id | String |

```JSON
{
	"RetCode":,
	"ErrMsg":,
	"TaskId":
	}
```

##### 示例请求

```Http
POST /fetch/ HTTP/1.1
Authorization:Authorization: UCloud this-is-my-public-key:AAAArandomsignature=
Content-Type:application/json
Content-Length: 159

{
	"Url": "http://xxx.xxx.xxx/xxx/movie.mp4",
	"Key":"movie.mp4",
	"JobId": "xxxxxxxxxxxxxxxxxxxx"
	"SuccessCallbackUrl":"http://xxx.xxx.xxx/xxx",
	"FailureCallbackUrl":"http://xxx.xxx.xxx/xxx"
}
```

##### 返回

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 122
Connection: keep-alive

{
	"RetCode":0,
	"ErrMsg":"success",
	"TaskId": "d4d62b79-b292-411a-a1f2-47369e2b532f"
}
```

#### 回调内容

您可以在请求中指定任务完成后的回调地址，US3SYNC会依照任务执行结果给相应的地址放松POST请求，请求内容示例如下：

##### Failure callback

```JSON
{
	"Code":1,
	"TaskId": "d4d62b79-b292-411a-a1f2-47369e2b532f",
	"Message":"We encountered an internal error.",
	"Resource":"http://xxx.xxx.xxx/xxx/movie.mp4",
}
```

##### Success callback

```JSON
{	
	"Code": 0,
	"TaskId": "d4d62b79-b292-411a-a1f2-47369e2b532f",
	"ETag":"xxxxxxxx",
	"Key":"xxxxxxxxxxxxxxx.mp4",
	"SHA1":"0bc51013e87869137a432200f57daf6affdd3d0c",
	"Size":638304718
}
```

## 主要功能

1. 支持从 s3，oss，qiniu，youpai，US3 云存储迁移数据到 US3 中。
2. 支持从 NAS 存储或者本地目录将数据迁移到 US3 中。
3. 支持指定 URL 资源列表将数据迁移到 US3 中。
4. 支持 web 管理，通过 web 管理迁移任务，迁移节点。
5. 支持源端管理
6. 支持配置任务定期执行
7. 支持失败文件列表导出

**注：暂不支持源端为归档类型的文件迁移到 US3。**

## 文件结构

```
US3SYNC
├── bin
│ |── master            # master 可执行程序
│ └── worker            # worker 可执行程序
├── conf
│ └── config.toml       # 配置文件
├── cert                # https证书
├── log                 # master日志文件存放路径
├── pika                # 依赖pika
└── console.sh          # 启动脚本
```

## 与原迁移工具对比

原迁移工具使用请参照：[原迁移工具](/ufile/tools/tools/ufile_import)

| US3SYNC              | ufile-import                       |
| ------------------------ | ---------------------------------- |
| 提供界面管理操作         | 只支持命令行操作                   |
| 配置文件整合为单个       | 多个配置文件                       |
| 数据不落盘，提高迁移效率 | 数据落盘，需要根据需要提供磁盘资源 |
| 使用 pika 缓存           | 使用 redis 缓存                    |
| 按分片粒度并发，带宽稳定 | 按文件粒度并发，对大文件迁移不友好 |
| 支持按照大小进行数据校验 | 不支持校验                         |

## 版本和运行环境

### 软件版本

当前版本：1.4.0

### 运行环境

- Linux：
  - CentOS 7.0 及以上 (可通过`cat /etc/redhat-release`查看)
  - Ubuntu 16.04 及以上 (可通过`cat /etc/issue`查看)