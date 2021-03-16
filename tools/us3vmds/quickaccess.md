# 快速上手

- [使用说明](#使用说明)
  - [参数配置](#参数配置)
  - [开始使用](#开始使用)
- [性能分析](#性能分析)
- [性能数据](#性能数据)
- [缺陷](#缺陷)


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

## 性能分析

us3vmds基于Go语言开发，自带了Go的profile功能，通过以下命令，可以在指定地址开启Go的运行时性能分析HTTP服务：

```
➜  bin  ✗ us3vmds pprof --open 127.0.0.1 8081
➜  bin  ✗ netstat -tunpl | grep 8081
tcp        0      0 127.0.0.1:8081          0.0.0.0:*               LISTEN      3312/us3vmds
➜  bin  ✗ us3vmds pprof --close // 关闭运行时性能分析HTTP服务
➜  bin  ✗ netstat -tunpl | grep 8081
```

Go的运行时分析可以参考Go的官方文档[Package pprof](https://golang.org/pkg/net/http/pprof/)

## 性能数据

### 硬件环境

| 部署服务                                                     | 数量 | 操作系统        | CPU  | 内存 | 数据盘 （数量/单盘容量/规格） |
| ------------------------------------------------------------ | ---- | --------------- | ---- | ---- | ----------------------------- |
| NameNode<br />ResourceManager<br />Zookeeper<br />JournalNode | 3    | Centos 7.6 64位 | 32C  | 128G | 1/100G/RSSD云盘               |
| DataNode<br />NodeManager                                    | 10   | Centos 7.6 64位 | 32C  | 128G | 10/300G/RSSD云盘              |

### 软件环境

| 软件      | 版本号 | 限制约束     |
| --------- | ------ | ------------ |
| Hadoop    | 2.8.5  | 标准社区版本 |
| Hive      | 2.3.7  | 标准社区版本 |
| Spark     | 2.4.3  | 标准社区版本 |
| Zookeeper | 3.5.6  | 标准社区版本 |

### 关键参数

#### Hadoop

| 配置文件      | 配置项          | 配置值 |
| ------------- | --------------- | ------ |
| hdfs-site.xml | dfs.replication | 3      |

### HiBench测试

HiBench是 Intel 开源的大数据基准测试工具，可帮助评估速度，吞吐量和系统资源利用率方面的不同大数据框架。 它包含一组Hadoop，Spark和流工作负载，包括Sort，WordCount，TeraSort，Repartition, Sleep，SQL，PageRank，Nutch indexing，Bayes，Kmeans，NWeight和enhanced DFSIO等。它还包含一些用于Spark流的流工作负载 ，Flink，Storm和Gearpump。HiBench中总共有29个工作负载。 工作负载分为6类，分别是micro，ml（machine learning），sql，graph，websearch和stream。

本次HiBench测试的版本为：

[v7.0.0](https://github.com/Intel-bigdata/HiBench/tree/HiBench-7.0)

公共配置信息为:

| 配置文件     | 配置项                              | 配置值 |
| ------------ | ----------------------------------- | ------ |
| hibench.conf | hibench.scale.profile               | mydata |
| hibench.conf | hibench.default.map.parallelism     | 40960  |
| hibench.conf | hibench.default.shuffle.parallelism | 10240  |
| spark.conf   | hibench.yarn.executor.num           | 50     |
| spark.conf   | hibench.yarn.executor.cores         | 4      |
| spark.conf   | spark.executor.memory               | 16g    |
| spark.conf   | spark.driver.memory                 | 8g     |

测试选择了其中的7个工作负载进行，且分别基于us3(只采用适配器工具)、us3vmds(适配器和us3vmds)、hdfs三种底层存储进行：

#### Micro

**sort**

测试参数调整为:

- hibench.sort.mydata.datasize: 3000000000000

> 该参数表示总体数据量，约为3TB

测试数据:

![](/images/hibench-micro.sort.png)

**terasort**

测试参数调整为:

- hibench.sort.mydata.datasize: 30000000000

> 该参数表示生成的多少行数据，结合源码可知，随机生成每行数据大小为100字节，所以为了跟micro.sort总体测试数据量保持一致设置为30000000000。

测试数据:

![](/images/hibench-micro.terasort.png)

**wordcoun**

测试参数调整为:

- hibench.dfsioe.mybigdata.read.number_of_files 44704
- hibench.dfsioe.mybigdata.read.file_size 64 // 64MiB
- hibench.dfsioe.mybigdata.write.number_of_files 44704
- hibench.dfsioe.mybigdata.write.file_size 64 // 64MiB

> 测试的读写数据量约为3TB

测试数据:

![](/images/hibench-micro.wordcount.png)

**dfsioe**

测试参数调整为:

- hibench.dfsioe.mybigdata.read.number_of_files 44704
- hibench.dfsioe.mybigdata.read.file_size 64 // 64MiB
- hibench.dfsioe.mybigdata.write.number_of_files 44704
- hibench.dfsioe.mybigdata.write.file_size 64 // 64MiB

> 测试的读写数据量约为3TB

测试数据:

![](/images/hibench-micro.dfsioe.png)


#### SQL

**scan**

测试参数调整为:

- hibench.scan.mybigdata.uservisits 20000000000
- hibench.scan.mybigdata.pages 100000000

测试数据:

![](/images/hibench-sql.scan.png)

**join**

测试参数调整为:

- hibench.scan.mybigdata.uservisits 20000000000
- hibench.scan.mybigdata.pages 100000000

测试数据:

![](/images/hibench-sql.join.png)

> 注意: Create rankings和Create uservisits任务同sql.scan，所以忽略

**aggregation**

测试参数调整为:

- hibench.scan.mybigdata.uservisits 20000000000
- hibench.scan.mybigdata.pages 100000000

测试数据:

![](/images/hibench-sql.aggregation.png)

> 注意: Create rankings和Create uservisits任务同sql.scan，所以忽略

总体来看，基于us3目前的方案相对原生分布式存储hdfs有一定差距，但基于us3vmds的方案在大部分场景性能有了大幅提高，后续也会不断改进，推出媲美甚至超越hdfs的方案。

## 缺陷

虽然us3vmds目前确实能给基于US3存储的大数据方案带来进一步性能提高，但us3vmds目前的设计还是有不少缺陷，主要在体现在几个方面：

1. 单点架构使得可靠性降低。不过us3vmds是无状态的设计，如果由于某些原因导致us3vmds退出，不会导致索引的丢失，只要快速重启，还是能根据us3的索引重建目录树物化视图，一般在服务过程中us3vmds退出10s内，不会对大数据任务产生影响，因为us3大数据适配器工具对错误做了一定时长的重试。只要及时保证us3vmds处于启动状态，就可以使得因us3vmds异常退出导致的可靠性问题大幅减少。当然由于us3vmds所在节点故障需要迁移而导致us3vmds需要更新服务地址影响时延相对会长，这个时候可以简单的修改配置，暂时改回只使用us3大数据适配器的方案。
2. 某些操作不能保证原子性。比如移动目录、删除目录，在us3vmds中的操作会变成批量操作这个目录前缀下的相关文件，中间任何一个文件即使加上了重试逻辑也可能会失败。目前us3vmds对这类问题只通过尽可能的重试来解决。
3. 非大数据集群写入数据，us3vmds索引不能保证强一致性。由于通过非us3大数据适配器方式(比如SDK)写入us3的数据不能及时通知us3vmds，所以us3vmds只能依赖大数据集群主动触发同步操作去同步最新索引，只能做到弱一致性。通过us3vmds方案中us3大数据适配器方式写入us3的数据，由于在写入us3成功后会主动通知us3vmds，所以us3vmds能及时更索引信息，做到强一致性。

因此目前us3vmds不满足ACID，只满足BASE。
