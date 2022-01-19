# 快速上手

- [视频教学](#视频教学)
- [使用说明](#使用说明)
  - [参数说明](#参数说明)
  - [Hadoop配置](#Hadoop配置)
  - [使用方式](#使用方式)
- [场景示例](#场景示例)

## 视频教学

观看以下视频，快速上手使用US3Hadoop

<video id="video" length=1000 width=800 controls="" preload="none" poster="https://static.ucloud.cn/1a66ee246c0ff52203d82c9f001b6b45.png">
      <source id="mp4" src="http://caozuozhinan.cn-bj.ufileos.com/录屏2 us3 Hadoop.mp4">
      </video>


## 使用说明

### 参数说明

| 属性Key                         | 属性说明                                                     | 默认值      |
| ------------------------------- | ------------------------------------------------------------ | ----------- |
| fs.us3.impl                     | US3对FileSystem的实现类名，请确保为:cn.ucloud.us3.fs.US3FileSystem | 无          |
| fs.AbstractFileSystem.us3.impl  | 对AbstractFileSystem 的实现类，固定为 cn.ucloud.us3.fs.US3Fs。 | 无          |
| fs.us3.access.key               | 访问US3的API公钥或者Token公钥                                | 无          |
| fs.us3.secret.key               | 访问US3的API私钥或者Token私钥                                | 无          |
| fs.us3.socket.recv.buffer       | US3读取流Buffer，在HiveSQL、Spark-SQL等场景,如果分析的数据格式是ORC，Parquet等,建议调小该参数，比如16348(16KB)，如果分析的数据格式是普通的文本格式,建议调大该参数，比如1048576(1MB) | 65536(64KB) |
| fs.us3.log.level                | US3大数据适配器日志级别，支持info, error, debug, trace级别。目前日志统一输出到标准输出 | info        |
| fs.us3.endpoint                 | US3内网域名后缀，如:ufile.cn-north-02.ucloud.cn。参考[地域和域名](https://docs.ucloud.cn/ufile/introduction/region)，域名需去除`www.`前缀。 | 无          |
| fs.us3.async.wio.use            | 单个流写入是否使用异步IO，可以提高写入速度，特别是对单个大文件有效，但会消耗一定CPU资源，可能会降低任务的并行度 | false       |
| fs.us3.async.wio.parallel       | 在fs.us3.async.wio.use为true情况下生效，表示单个文件写入4MB分片的最大并行 | 2           |
| fs.us3.metadata.use             | 是否启用索引缓存服务器，来加速US3索引性能，该服务需要使用者管理，目前正在公测中 | false       |
| fs.us3.metadata.host            | 在fs.us3.metadata.usetrue情况下生效，可以直接指定IP:Port的地址形式，这种方式不需要配置/etc/hosts文件。用户也可以指定自定义域名，需要在/etc/hosts中配置解析地址，或者配置DNS解析。该参数处于测试中。 | 无          |
| fs.us3.metadata.zookeeper.addrs | 高可用vmds集群方案中使用的zookeeper节点信息，参数格式为以","分割的zookeeper服务器地址和端口号，例如：uhadoop-q4mbrxk2-master1:2181,uhadoop-q4mbrxk2-master2:2181,uhadoop-q4mbrxk2-core1:2181。如果您配置了fs.us3.metadata.host参数，则本参数无效。**该功能需要US3适配器版本>=1.3.0。** | 无          |
| fs.us3.generate.md5             | 默认为`false`，如果为`true`则在写入US3时，客户端会计算一个MD5，并在最后会以`md5-hash`为key，base64编码的md5值为value写入到文件的元数据中。开启会增加>=30%的写入时延。<br> **该功能需要US3适配器的版本>=1.0.2** | false       |

### Hadoop配置

1
需要在core-site.xml中配置以上相关配置项，如：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://192.168.0.221:9000</value>
  </property>
  ......
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>/data/hd3/datanode</value>
  </property>
  <property>
    <name>fs.us3.impl</name>
    <value>cn.ucloud.us3.fs.US3FileSystem</value>
  </property>
  <property>
    <name>fs.AbstractFileSystem.us3.impl</name>
    <value>cn.ucloud.us3.fs.US3Fs</value>
  </property>
  <property>
    <name>fs.us3.access.key</name>
    <value>TOKEN_xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</value>
  </property>
  <property>
    <name>fs.us3.secret.key</name>
    <value>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</value>
  </property>
  <property>
    <name>fs.us3.socket.recv.buffer</name>
    <value>16348</value>
  </property>
  <property>
    <name>fs.us3.thread.pool.size</name>
    <value>8</value>
  </property>
  <property>
    <name>fs.us3.log.level</name>
    <value>info</value>
  </property>
  <property>
    <name>fs.us3.endpoint</name>
    <value>ufile.cn-north-02.ucloud.cn</value>
  </property>
  <property>
    <name>fs.us3.async.wio.use</name>
    <value>false</value>
  </property>
  <property>
    <name>fs.us3.async.wio.parallel</name>
    <value>2</value>
  </property>
  <property>
    <name>fs.us3.metadata.use</name>
    <value>false</value>
  </property>
  <property>
    <name>fs.us3.metadata.host</name>
    <value></value>
  </property>
  <property>
    <name>fs.us3.metadata.zookeeper.addrs</name>
    <value>uhadoop-q4mbrxk2-master1:2181,uhadoop-q4mbrxk2-master2:2181,uhadoop-q4mbrxk2-core1:2181</value>
  </property>
	......
</configuration>
```

### 使用方式

#### Hadoop文件系统访问

使用格式为`hadoop fs -ls us3://<bucket name>/<path>`。使用式样如下:

```
[root@task10 ~]# hadoop fs -ls us3://bigdata-us3/HiBench/Aggregation/Input/rankings | more
Found 10241 items
-rw-r--r--   1 root supergroup          0 2021-01-26 13:46 us3://bigdata-us3/HiBench/Aggregation/Input/rankings/_SUCCESS
-rw-r--r--   1 root supergroup    6375702 2021-01-26 13:30 us3://bigdata-us3/HiBench/Aggregation/Input/rankings/part-00000
-rw-r--r--   1 root supergroup    6379753 2021-01-26 13:30 us3://bigdata-us3/HiBench/Aggregation/Input/rankings/part-00001
-rw-r--r--   1 root supergroup    6372962 2021-01-26 13:30 us3://bigdata-us3/HiBench/Aggregation/Input/rankings/part-00002
......
```

**其他使用方式同hdfs操作**

#### Hive使用US3

在创建外部表的时候指定location 为对应的US3路径即可，如`... location 'us3://<bucket name>/hive/tpc-ds/1002-orc/customer'`

#### Spark使用US3

无论是通过spark-submit还是pyspark、spark-shell、spark-sql等读写US3时，需要指定文件路径的格式为: `us3://<bucket name>/path`

#### Flink使用US3

只需指定US3格式的路径即可；

**注意**

- 已有存储空间的文件对象会返回默认权限；
- 不支持包含特殊字符的文件名，如`:`；

## 场景示例

假设需要把HDFS集群的/var目录数据备份至US3的bigdata-us3存储空间的/distcp/前缀下，可以在安装好前面的适配器后通过hadoop自带的distcp执行拷贝，如：

```
[root@master1 tools]# hadoop distcp -bandwidth 15 -m 20 -skipcrccheck -update -pugp  /var us3://bigdata-us3/distcp/var
21/01/26 16:51:50 INFO tools.DistCp: Input Options: DistCpOptions{atomicCommit=false, syncFolder=true, deleteMissing=false, ignoreFailures=false, overwrite=false, skipCRC=true, blocking=true, numListstatusThreads=0, maxMaps=20, mapBandwidth=15, sslConfigurationFile='null', copyStrategy='uniformsize', preserveStatus=[USER, GROUP, PERMISSION], preserveRawXattrs=false, atomicWorkPath=null, logPath=null, sourceFileListing=null, sourcePaths=[/var], targetPath=us3://bigdata-us3/distcp/var, targetPathExists=false, filtersFile='null'}
21/01/26 16:51:51 INFO tools.SimpleCopyListing: Paths (files+dirs) cnt = 9226; dirCnt = 851
21/01/26 16:51:51 INFO tools.SimpleCopyListing: Build file listing completed.
21/01/26 16:51:51 INFO Configuration.deprecation: io.sort.mb is deprecated. Instead, use mapreduce.task.io.sort.mb
21/01/26 16:51:51 INFO Configuration.deprecation: io.sort.factor is deprecated. Instead, use mapreduce.task.io.sort.factor
21/01/26 16:51:51 INFO tools.DistCp: Number of paths in the copy list: 9226
21/01/26 16:51:52 INFO tools.DistCp: Number of paths in the copy list: 9226
21/01/26 16:51:53 INFO mapreduce.JobSubmitter: number of splits:25
21/01/26 16:51:53 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1611108331895_0847
21/01/26 16:51:54 INFO impl.YarnClientImpl: Submitted application application_1611108331895_0847
21/01/26 16:51:54 INFO mapreduce.Job: The url to track the job: http://master2:23188/proxy/application_1611108331895_0847/
21/01/26 16:51:54 INFO tools.DistCp: DistCp job-id: job_1611108331895_0847
21/01/26 16:51:54 INFO mapreduce.Job: Running job: job_1611108331895_0847
21/01/26 16:52:01 INFO mapreduce.Job: Job job_1611108331895_0847 running in uber mode : false
21/01/26 16:52:01 INFO mapreduce.Job:  map 0% reduce 0%
21/01/26 16:52:18 INFO mapreduce.Job:  map 1% reduce 0%
21/01/26 16:52:19 INFO mapreduce.Job:  map 3% reduce 0%
21/01/26 16:52:20 INFO mapreduce.Job:  map 6% reduce 0%
21/01/26 16:53:07 INFO mapreduce.Job:  map 7% reduce 0%
21/01/26 16:53:31 INFO mapreduce.Job:  map 8% reduce 0%
...
```

**注意**

- `-p`参数不要带上`b`，因为US3实际的块大小跟HDFS不一致，如果重新执行该命令会导致已有文件会重新被覆盖上传；
- `-skipcrccheck -update`请保证开启，因为US3的完整性校验通过另一套机制完成；

如果不想安装适配器即可进行备份或者文件系统操作（避免侵入已有服务带来的风险），可以按照如下格式进行执行操作:

```
hadoop fs|distcp|... -Dfs.us3.impl=cn.ucloud.us3.fs.US3FileSystem \
                -Dfs.us3.access.key=${访问US3的API公钥或者Token公钥} \
                -Dfs.us3.secret.key=${访问US3的API私钥或者Token私钥} \
                -Dfs.us3.socket.recv.buffer=65536 \
                -Dfs.us3.log.level=${日志级别} \
                -Dfs.us3.endpoint=${访问点} \
                -Dfs.us3.async.wio.use=${是否开启写入异步IO} \
                -Dfs.us3.async.wio.parallel=${异步写入IO并行度} \
                -Dmapreduce.job.user.classpath.first=true \
                -Dmapreduce.task.classpath.user.precedence=true \
                -libjars ${适配器jar包路径} \
                ${对应hadoop子命令的执行参数} \
```

**配置参数参考参数说明**

如拉取US3的某个目录:

```
[root@master1 tools]# hadoop fs -Dfs.us3.impl=cn.ucloud.us3.fs.US3FileSystem -Dfs.us3.access.key=TOKEN_7468973e-d192-xxxx-xxxx-xxxxxxxxxxxx -Dfs.us3.secret.key=9a632eb8-3938-xxxx-xxxx-xxxxxxxxxxxx -Dfs.us3.socket.recv.buffer=65536 -Dfs.us3.thread.pool.size=8 -Dfs.us3.log.level=info -Dfs.us3.endpoint=internal-cn-bj.ufileos.com -Dfs.us3.bucket.bigdata.only=false -Dfs.us3.async.wio.use=true -Dfs.us3.async.wio.parallel=2 -Dmapreduce.job.user.classpath.first=true -Dmapreduce.task.classpath.user.precedence=true -libjars ./us3-bigdata-adaptor-2.8.5.0.jar -ls us3://bigdata-us3/distcp/var/log/hadoop-yarn/apps/root/logs
Found 846 items
drwxrwxrwx   - root supergroup          0 2021-01-26 16:52 us3://bigdata-us3/distcp/var/log/hadoop-yarn/apps/root/logs/application_1611108331895_0001
drwxrwxrwx   - root supergroup          0 2021-01-26 16:53 us3://bigdata-us3/distcp/var/log/hadoop-yarn/apps/root/logs/application_1611108331895_0002
drwxrwxrwx   - root supergroup          0 2021-01-26 16:52 us3://bigdata-us3/distcp/var/log/hadoop-yarn/apps/root/logs/application_1611108331895_0003
drwxrwxrwx   - root supergroup          0 2021-01-26 16:53 us3://bigdata-us3/distcp/var/log/hadoop-yarn/apps/root/logs/application_1611108331895_0004
drwxrwxrwx   - root supergroup          0 2021-01-26 16:53 us3://bigdata-us3/distcp/var/log/hadoop-yarn/apps/root/logs/application_1611108331895_0005
drwxrwxrwx   - root supergroup          0 2021-01-26 16:53 us3://bigdata-us3/distcp/var/log/hadoop-yarn/apps/root/logs/application_1611108331895_0006
drwxrwxrwx   - root supergroup          0 2021-01-26 16:53 us3://bigdata-us3/distcp/var/log/hadoop-yarn/apps/root/logs/application_1611108331895_0007
...
```
