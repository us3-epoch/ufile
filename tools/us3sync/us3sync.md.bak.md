# US3SYNC 迁移工具

## 概述

US3SYNC 是一款将不同源的数据同步到 US3 的迁移工具。通过将 US3SYNC 部署在本地或者云主机中，可以便捷地从本地或者其他云环境中将数据迁移到 US3 存储空间。

### 工作原理

![](http://ufile-release.cn-bj.ufileos.com/us3sync/doc/structure.jpg)

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

### 运行环境

US3SYNC 支持 linux 环境部署。

US3SYNC 依赖 ssh，scp，expect，rsync 命令，确保预装这些命令，使用 yum 源做包管理可以使用以下命令安装：

```
yum install -y ssh scp expect rsync
```

### 下载连接

可以通过以下命令下载工具包。

```
curl -o US3SYNC.tgz "http://ufile-release.cn-bj.ufileos.com/US3SYNC/US3SYNC.tgz"
```

### 主要功能

1. 支持从 s3，oss，qiniu，youpai，US3 云存储迁移数据到 US3 中。
2. 支持从 NAS 存储或者本地目录将数据迁移到 US3 中。
3. 支持指定 URL 资源列表将数据迁移到 US3 中。
4. 支持 web 管理，通过 web 管理迁移任务，迁移节点。

**注：暂不支持源端为归档类型的文件迁移到 US3。**

### 文件结构

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

### 使用方式

#### 工具部署

```
# 下载并解压
curl -o US3SYNC.tgz "http://ufile-release.cn-bj.ufileos.com/US3SYNC/US3SYNC.tgz"
tar xzf US3SYNC.tgz
cd ./US3SYNC

# 启动master服务
./console.sh start
请设置缓存服务监听端口[9000]:
请设置缓存服务密码[user-passwd]:
# 云主机一般绑定EIP，建议ip地址使用0.0.0.0
请设置web 服务监听地址[0.0.0.0:80]:
# 内部通信地址不提供外网服务，建议ip地址使用机器内网ip
请设置内部通信监听地址[x.x.x.x:8080]:
请设置报错重试次数[10]:
请设置web 登录使用的用户名[root]: 
请设置web 登录使用的密码[passwd]: 
US3SYNC start success!

# 查看master服务
./console.sh show

# 结束master服务
./console.sh stop
```
- 验证
```
查看进程是否正常启动
./console.sh show
```

#### 添加worker节点

- 界面操作

1. 服务启动后，在浏览器中打开：https://<web服务监听IP>:<web服务监听端口>/<br>**注：使用云主机部署迁移服务时，这里需要使用EIP，而不是0.0.0.0。**
2. 页面登录，使用启动时设置的用户名和密码。
3. 添加工作节点，参考 [创建节点界面说明](#创建节点界面说明)，需要为每个节点提供唯一工作路径。
   每个工作节点需要提供唯一工作路径，如果路径不存在会自动创建对应目录。<br>**注：建议使用内网ip。**

- 命令行操作

```
# 部署worker节点，可以部署多个，每个节点需要一个单独目录
mkdir -p node/bin node/log
cp bin/worker/US3SYNC ./node/
# 启动worker节点
cd node
nohup ./bin/US3SYNC -T <threads> <master-inner-ip>:<master-inner-port> --cache-addr <ip:port> --cache-pass <password> > log/worker.log 2>&1 &
```

- 验证

```
查看进程是否正常启动
ps -ef | grep US3SYNC
```

#### 创建任务

- 界面操作

1. 创建任务，参考 [创建任务界面说明](#创建任务界面说明)，点击创建任务按钮，在对话框中填写对应信息，点击确定
说明：
迁移过程会将指定的源端前缀下的文件迁移到指定的目的端前缀下面，例如：
1. 源端前缀指定了：A/ 目的端前缀指定了 B/
此时会将源端 A/ 下的所有文件迁移到目的端 B/ 下
如果源端有一个文件A/a.txt 迁移后在目录端会有 B/a.txt
2. 源端前缀指定了：A/ 目的端前缀指定了空路径
此时会将源端 A/ 下的所有文件迁移到目的端根目录下
如果源端有一个文件A/a.txt 迁移后在目录端根目录下会有 a.txt

- 命令行操作（不推荐）

```
# 命令行操作方式需要启动master节点之前，在配置文件中配置好迁移任务，然后执行以下命令启动master节点：
nohup ./bin/master/US3SYNC -C conf/config.toml --cache-addr <ip:port> --cache-pass <password> --server-addr <ip:port> --inner-addr <ip:port> --login-user <user> --login-pass <password> --retry-count <count> import > log/master.log 2>&1 &
```

#### 启动迁移

- 界面操作

1. 参考 [创建任务界面说明](#创建任务界面说明)，点击开启迁移按钮

- 命令行操作
命令行操作方式启动maste节点后自动开始迁移，无需操作

#### 跳过已存在文件

- 界面操作

当目的端已经有部分文件，并且迁移过程不想覆盖已经存在的文件，点击开始迁移按钮弹出是否覆盖已有文件时，选择否。

- 命令行操作

命令行欲覆盖已存在的文件，启动时添加参数 --overwrite。

#### 重试
- 界面操作

1. 如果迁移后有失败记录，可以对迁移失败的文件进行重试，参考 [创建任务界面说明](#创建任务界面说明)，点击管理-重试按钮

- 命令行操作（不推荐）
```
命令行操作方式需要执行以下命令启动重试：
nohup ./bin/master/US3SYNC -C conf/config.toml --cache-addr <ip:port> --cache-pass <password> --server-addr <ip:port> --inner-addr <ip:port> --login-user <user> --login-pass <password> --retry-count <count> import --retry > log/master.log 2>&1 &
```

#### 校验
- 界面操作

1. 当迁移完成并且没有迁移失败的记录，可以启动校验，参考 [任务管理界面说明](#任务管理界面说明) ，点击开始校验按钮
2. 校验完成后如果有失败记录，可以对校验失败的文件重试，参考 [任务管理界面说明](#任务管理界面说明)，点击管理-重试按钮

- 命令行操作（不推荐）
```
命令行操作方式需要执行以下命令启动校验：
nohup ./bin/master/US3SYNC -C conf/config.toml --cache-addr <ip:port> --cache-pass <password> --server-addr <ip:port> --inner-addr <ip:port> --login-user <user> --login-pass <password> --retry-count <count> audit > log/master.log 2>&1 &
命令行操作方式需要执行以下命令启动校验重试：
nohup ./bin/master/US3SYNC -C conf/config.toml --cache-addr <ip:port> --cache-pass <password> --server-addr <ip:port> --inner-addr <ip:port> --login-user <user> --login-pass <password> --retry-count <count> audit --retry > log/master.log 2>&1 &
```

### 配置文件说明
无法提供web服务时，可以通过配置文件配置任务
```
# 命令行操作方式使用以下配置，不使用忽略
# [job]
# name = ""
# type = "" # 任务类型，可选：aws, oss, qiniu, us3, uarchive, local, http

# [job.source]
# access-key = ""
# bucket = ""
# endpoint = ""
# prefix = ""
# secret-key = ""
# # aws
# region = ""
# # http
# http-addr = "" # 待迁移地址列表文件，该文件需公网可下载

# [job.destine]
# access-key = ""
# bucket = ""
# endpoint = ""
# prefix = ""
# secret-key = ""
# storage-class = "" # 迁移到us3后使用的存储类型，可选值：STANDARD, IA, ARCHIVE，置空代表与源保持一致
```

### 缓存说明
US3SYNC 使用 pika 提供缓存服务。主要缓存以下信息：

- 任务详细信息
- 迁移统计信息
- 迁移记录

### 迁移类型说明

| 迁移类型 | 说明                                                           |
| -------- | -------------------------------------------------------------- |
| aws      | 实现基于 s3 接口的迁移，支持 s3 协议的云平台皆可基于此类型迁移 |
| oss      | 实现 oss 云存储接口                                            |
| qiniu    | 实现 qiniu 云存储接口                                          |
| youpai   | 实现 youpai 云存储接口                                         |
| us3      | 实现 us3 云存储接口                                            |
| uarchive | 实现 uarchive 云存储接口                                       |
| NAS      | 实现 NAS 系统或者本地文件迁移                                  |
| URL      | 实现 基于 URL 列表迁移                                         |

#### AWS
aws 源需要额外提供待迁移空间的 region 字段，参考：https://docs.aws.amazon.com/zh_cn/general/latest/gr/s3.html  
第三方实现S3协议参考对应厂商S3文档说明

#### local
local 源需要提供待迁移目录的路径

#### http
http 源需要提供待迁移文件列表的 url 地址
待迁移文件列表格式如下，每行代表一个待迁移资源
>
> 文件url地址  
> 文件url地址
> 

### 界面说明

#### 登录界面说明

![](http://ufile-release.cn-bj.ufileos.com/us3sync/doc/login.png)

#### 任务管理界面说明

![](http://ufile-release.cn-bj.ufileos.com/us3sync/doc/job-detail.png)

```
- 任务操作：
开始迁移：开始文件迁移
开始校验：基于大小校验源端与目录端文件，会从源端与目的端全量拉取文件列表对比
管理-重试：迁移文件后有失败记录，或者，校验后有失败记录，都可以通过改按钮将失败文件重新迁移
管理-删除：删除任务，会清理任务缓存记录
管理-克隆：打开创建任务界面，并填写当前任务信息

- 统计信息：
=================================
点击迁移按钮后：
迁移总数:   此次迁移文件总数
等待迁移: 处于待迁移队列的文件个数
正在迁移: 正在执行迁移的文件个数
迁移成功:   已经迁移成功的文件个数
迁移失败:   已经迁移失败的文件个数
=================================
点击校验按钮后：
校验总数:   此次校验文件总数
校验成功:   校验成功的文件个数
校验失败:   校验失败的文件个数
=================================
```

#### 创建任务界面说明

![](http://ufile-release.cn-bj.ufileos.com/us3sync/doc/create-job.png)

#### 节点管理界面说明

![](http://ufile-release.cn-bj.ufileos.com/us3sync/doc/node-detail.png)

#### 创建节点界面说明

![](http://ufile-release.cn-bj.ufileos.com/us3sync/doc/create-node.png)

### 与原迁移工具对比

原迁移工具使用请参照：[原迁移工具](/ufile/tools/tools/ufile_import)

| US3SYNC              | ufile-import                       |
| ------------------------ | ---------------------------------- |
| 提供界面管理操作         | 只支持命令行操作                   |
| 配置文件整合为单个       | 多个配置文件                       |
| 数据不落盘，提高迁移效率 | 数据落盘，需要根据需要提供磁盘资源 |
| 使用 pika 缓存           | 使用 redis 缓存                    |
| 按分片粒度并发，带宽稳定 | 按文件粒度并发，对大文件迁移不友好 |
| 支持按照大小进行数据校验 | 不支持校验                         |

### 常见问题

#### master部署后，浏览器无法看到界面？
通常为防火墙屏蔽了相应端口，请检查机器防火墙的web服务监听端口是否开启。

#### worker部署后，启动任务后正在迁移的任务一直显示0？
请检查worker进程是否成功启动，如未正常启动，通常为worker节点与master节点网络不通导致。
