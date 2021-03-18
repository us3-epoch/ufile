
# 性能数据与分析

us3vmds基于Go语言开发，自带了Go的profile功能，通过以下命令，可以在指定地址开启Go的运行时性能分析HTTP服务：

```
➜  bin  ✗ us3vmds pprof --open 127.0.0.1 8081
➜  bin  ✗ netstat -tunpl | grep 8081
tcp        0      0 127.0.0.1:8081          0.0.0.0:*               LISTEN      3312/us3vmds
➜  bin  ✗ us3vmds pprof --close // 关闭运行时性能分析HTTP服务
➜  bin  ✗ netstat -tunpl | grep 8081
```

Go的运行时分析可以参考Go的官方文档[Package pprof](https://golang.org/pkg/net/http/pprof/)

## 测试环境
### 硬件环境

| 部署服务                                                     | 数量 | 操作系统        | CPU  | 内存 | 数据盘 （数量/单盘容量/规格） |
| ------------------------------------------------------------ | ---- | --------------- | ---- | ---- | ----------------------------- |
| NameNode<br>ResourceManager<>Zookeeper<br>JournalNode | 3    | Centos 7.6 64位 | 32C  | 128G | 1/100G/RSSD云盘               |
| DataNode<br>NodeManager                                    | 10   | Centos 7.6 64位 | 32C  | 128G | 10/300G/RSSD云盘              |

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

## 测试数据

测试选择了其中的7个工作负载进行，且分别基于us3(只采用适配器工具)、us3vmds(适配器和us3vmds)、hdfs三种底层存储进行：

### Micro

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


### SQL

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

# 缺陷

虽然us3vmds目前确实能给基于US3存储的大数据方案带来进一步性能提高，但us3vmds目前的设计还是有不少缺陷，主要在体现在几个方面：

1. 单点架构使得可靠性降低。不过us3vmds是无状态的设计，如果由于某些原因导致us3vmds退出，不会导致索引的丢失，只要快速重启，还是能根据us3的索引重建目录树物化视图，一般在服务过程中us3vmds退出10s内，不会对大数据任务产生影响，因为us3大数据适配器工具对错误做了一定时长的重试。只要及时保证us3vmds处于启动状态，就可以使得因us3vmds异常退出导致的可靠性问题大幅减少。当然由于us3vmds所在节点故障需要迁移而导致us3vmds需要更新服务地址影响时延相对会长，这个时候可以简单的修改配置，暂时改回只使用us3大数据适配器的方案。
2. 某些操作不能保证原子性。比如移动目录、删除目录，在us3vmds中的操作会变成批量操作这个目录前缀下的相关文件，中间任何一个文件即使加上了重试逻辑也可能会失败。目前us3vmds对这类问题只通过尽可能的重试来解决。
3. 非大数据集群写入数据，us3vmds索引不能保证强一致性。由于通过非us3大数据适配器方式(比如SDK)写入us3的数据不能及时通知us3vmds，所以us3vmds只能依赖大数据集群主动触发同步操作去同步最新索引，只能做到弱一致性。通过us3vmds方案中us3大数据适配器方式写入us3的数据，由于在写入us3成功后会主动通知us3vmds，所以us3vmds能及时更索引信息，做到强一致性。

因此目前us3vmds不满足ACID，只满足BASE。
