# 快速上手

- [使用说明](#使用说明)
  - [参数配置](#参数配置)
  - [开始使用](#开始使用)

## 使用说明

### 参数配置

有两种参数配置方式。通过交互式命令配置：

```shell
➜  bin  ✗ us3vmds config
输入监听IP地址[当前为: 127.0.0.1]:192.168.185.220 // 如果需要修改地址则填入修改值，否则直接输入enter采用默认值127.0.0.1
监听IP地址修改为: 192.168.185.220
输入监听端口[当前为: 5888]:6666 // 如果需要修改端口则填入修改值，否则直接输入enter采用默认值5888
监听IP地址修改为: 6666
输入API/Token公钥[当前为: ]:xxxx-xxxx-xxxx-xxxx // 如果需要修改公钥则填入修改值，否则直接输入enter，默认为空
API/Token公钥修改为: xxxx-xxxx-xxxx-xxxx
输入API/Token私钥[当前为: ]:yyyy-yyyy-yyyy-yyyy // 如果需要修改私钥则填入修改值，否则直接输入enter，默认为空
API/Token私钥修改为: yyyy-yyyy-yyyy-yyyy
输入存储空间Bucket[当前为: ]:bigdata-us3 // 如果需要修改存储空间填入修改值，否则直接输入enter，默认为空
存储空间Bucket修改为: bigdata-us3
输入访问端点Endpoint[当前为: ]:ufile.cn-north-04.ucloud.cn // 如果需要修改endpoint填入修改值，否则直接输入enter，默认为空
访问端点Endpoint修改为: ufile.cn-north-04.ucloud.cn
输入日志路径[当前为: /Users/rick.wu/.us3vmds]:/var/log/us3vmds // 如果需要修改日志路径填入修改值，否则直接输入enter，默认为/Users/rick.wu/.us3vmds
日志路径修改为: /var/log/us3vmds
输入日志级别(debug|error|info)[当前为: info]:debug // 如果需要修改日志级别则填入修改值，否则直接输入enter采用默认值info，建议始终采用debug级别，方便问题跟踪
日志级别修改为: debug
输入热点路径同步US3周期(单位:s)[当前为: 180 s]:3600 // 如果需要修改热点路径自动同步周期值则填入修改值，否则直接输入enter采用默认值180，如果只考虑分析从大数据集群写入的数据时，建议调大该值，避免频繁同步带来的性能开销
热点路径同步US3周期: 3600
```

通过文件配置，us3vmds使用配置文件路径始终在`$HOME/.us3vmds/us3vmds.yaml`，配置参数有：

|      属性Key      | 属性说明                                                     |     默认值     |
| :---------------: | :----------------------------------------------------------- | :------------: |
|     accesskey     | 访问US3的API公钥或者Token公钥                                |       无       |
|     secretkey     | 访问US3的API私钥或者Token私钥                                |       无       |
|      bucket       | 存储空间名                                                   |       无       |
|     endpoint      | US3内网域名后缀，如:ufile.cn-north-04.ucloud.cn。[参考这里](https://docs.ucloud.cn/ufile/introduction/region)，注意要去掉“www.”。 |       无       |
|       host        | us3vmds监听的IP地址                                          |   127.0.0.1    |
|       port        | us3vmds监听的端口                                            |       80       |
|      logdir       | 输出的日志路径，日志名固定为us3mvds.log或者历史日志名格式如us3vmds-2021-02-23T02-32-21.424.log，建议该日志路径至少有100G以上存储容量 | $HOME/.us3vmds |
|     loglevel      | 日志基本，可填:debug，error，info，建议目前调成debug方便后续问题跟踪 |      info      |
| hotspotsyncperiod | 热点路径同步周期，如果只考虑分析从大数据集群写入的数据时，建议调大该值，避免频繁同步带来的性能开销 |  180(单位:s)   |
|   isbigdataonly   | **如果只考虑分析从大数据集群写入的数据时，强烈建议该参数置为true以提高getFileStatus性能**，该参数在交互式命令中不显示需要手动修改配置文件 |     false      |

对应的配置文件例子如下:

```shell
➜  bin  ✗ cat $HOME/.us3vmds/us3vmds.yaml
accesskey: TOKEN_7468973e-d192-4378-8253-xxxxxxxx
bucket: bigdata-us3
endpoint: internal-cn-bj.ufileos.com
host: 172.16.16.237
hotspotsyncperiod: 3600
logdir: /data/us3vmds/log
loglevel: debug
port: 80
secretkey: 9a632eb8-3938-43a0-a7e1-xxxxxxxx
isbigdataonly: true
```

还可以通过命令展示配置信息:

```shell
➜  bin  ✗ us3vmds config --cat
配置路径为: /root/.us3vmds/us3vmds.yaml
监听IP地址为: 172.16.16.237
监听端口为: 80
API/Token公钥: TOKEN_7468973e-d192-4378-8253-xxxxxxxx
API/Token私钥: 9a632eb8-3938-43a0-a7e1-xxxxxxxx
存储空间Bucket: bigdata-us3
访问端点Endpoint: internal-cn-bj.ufileos.com
日志路径为: /data/us3vmds/log
日志级别为: debug
热点路径同步US3周期: 3600s
```

### 开始使用

#### 后台启动

```shell
➜  bin  ✗ us3vmds start
```

#### 停止重启

```shell
➜  bin  ✗ us3vmds restart
```

#### 停止

```shell
➜  bin  ✗ us3vmds stop
```

如果需要调试，也可以：

#### 前台启动

```shell
➜  bin  ✗ us3vmds start -f
2021-02-23T20:10:19.454+0800    DEBUG   mds/cache.go:49 [uuid:70a82150-3bee-4bda-8bb6-01401a05c1e8] insert / to cach
2021-02-23T20:10:19.455+0800    INFO    mds/sync_cache.go:418 hotspot sync period set 3600 seconds
...
```

当启动了us3vmds服务后，通过hadoop操作US3，并享受us3vmds带来的索引加速能力。除了以上功能操作，us3vmds还有以下功能：

#### 热点路径强制同步

```shell
➜  bin  ✗ us3vmds sync / /HiBench2 // 这里强制us3vmds对/和/HiBench2目录进行同步
```

**注意: 该同步路径必须已经在us3vmds中加载过，如果不确定可以先通过hadoop命令对该路径进行拉取**

