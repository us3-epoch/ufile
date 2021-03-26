
# 快速上手

- [参数说明](#参数说明)
- [使用同步](#使用同步)
- [使用校验](#使用校验)
- [增量迁移](#增量迁移)
- [缺陷](#缺陷)

## 参数说明

```shell
hadoop jar us3-bigdata-adaptor-2.8.5-1.1.0.jar cn.ucloud.us3.fs.distcp.DistCp
usage: distcp OPTIONS [source_path...] <target_path>
              OPTIONS
 -workspace <arg>   the workspace keeps a directory of state data for the
                    entire verification synchronization phase
 -modtime           check the modification time.when the modification time
                    of the source end is later than the destination end,
                    it is considered that it needs to be copied
 -checksum <arg>    to calculate the checksum, 'randome' only compares the
                    head and tail 4MB checksums, and randomly takes 4 1MB
                    fragments in the middle of the file for verification.
                    'all' will calculate the checksum of the entire file
                    and compare it. not on by default
 -algorithm <arg>   checksum algorithm, the default is crc32, you can also
                    choose'md5'
 -dump <arg>        Display the corresponding input information, input,
                    checkout, cpout
 -enforce           If the status result of the check or copy exists,
                    whether to enforce the check or copy
 -onlycheck         Only get the list of files that fail the integrity
                    check, no need to copy
 -skipcheck         skip all checks. Including file size, modification
                    time, checksum
 
```

- -workspace ： 该参数为必填项，会在该参数指定的目录中创建`input`，`checkout`，`cpout`三个目录，三个目录分别保存各个阶段的一些状态信息，`input`负责保留校验阶段的输入信息，该信息由执行程序输入的参数转换而来，`checkout`负责保留校验阶段输出的待同步文件结果，并作为同步阶段的输入，`cpout`保存的是同步阶段失败的文件，如果文件中的记录为空，则证明整个同步是成功的。
- -modtime: 可选，如果开启则会启用`目的修改时间是否最新`的校验策略；
- -checksum: 可选，如果开启则会启用`checksum校验`的校验策略，其值为`all`则会进行文件内容全量校验，为`randome`则会进行文件内容的抽样校验；
- -algorithm: 可选，该参数用于选择校验算法，目前只提供`md5`，`crc32c`，如果考虑校验时延，可以采用`crc32c`，默认为`crc32c`
- -dump: 用于列取workspace中`input`，`checkout`，`cpout`中的信息。
- -enforce: 一般在进行过校验或同步阶段后，不能再次执行，但该参数可以强制再次进行校验和同步；
- -onlycheck: 该参数会只有校验阶段，没有同步阶段；
- -skipcehck: 该参数会跳过校验阶段，只进行同步阶段；

此外还需要通过GenericOptionsParser的`-D`选项传入以下适配器的关键参数:

- fs.us3.imp: 始终为`cn.ucloud.us3.fs.US3FileSystem`。
- fs.us3.access.key: API或Token公钥。
- fs.us3.secret.key: API或Token私钥。
- fs.us3.endpoint: US3内网域名后缀，如:ufile.cn-north-04.ucloud.cn。参考[地域和域名](https://docs.ucloud.cn/ufile/introduction/region)，域名需去除`www.`前缀。
- fs.us3.async.wio.use: 单个流写入是否使用异步IO，可以提高写入速度，特别是对单个大文件有效，但会消耗一定CPU资源，可能会降低任务的并行度，默认为`false`。
- fs.us3.async.wio.parallel: 在fs.us3.async.wio.use为true情况下生效，表示单个文件写入4MB分片的最大并行，默认为2。
- fs.us3.metadata.use: 是否启用索引缓存服务器，来加速US3索引性能，该服务需要使用者管理，目前正在公测中。
- fs.us3.metadata.host: 在fs.us3.metadata.usetrue情况下生效，可以直接指定IP:Port的地址形式，这种方式不需要配置/etc/hosts文件。用户也可以指定自定义域名，需要在/etc/hosts中配置解析地址，或者配置DNS解析。该参数处于测试中。
- fs.us3.generate.md5: 默认为`false`，如果为`true`则在写入US3时，客户端会计算一个MD5，并在最后会以`md5-hash`为key，md5值为value写入到文件的元数据中。开启会增加写入时延。该功能需要US3适配器的版本>=1.0.2
- mapreduce.job.user.classpath.first: 始终为`true`。
- mapreduce.task.classpath.user.precedence: 始终为`true`。

最后还需通过`-libjars`选项传入适配器jar包路径。

## 使用同步

例如同步hdfs的`/var/log/hadoop-yarn/apps/root/logs/application_1613870792268_0067 目录下文件`到us3的存储空间`bigdata-us3`的`hdfs_backup`前缀下

```shell
➜  bin  ✗ hadoop jar ./us3-bigdata-adaptor-2.8.5-1.1.0.jar \
					cn.ucloud.us3.fs.distcp.DistCp \
					-Dfs.us3.impl=cn.ucloud.us3.fs.US3FileSystem \
					-Dfs.us3.access.key=TOKEN_7468973e-d192-4378-8253-xxxxxx \
					-Dfs.us3.secret.key=9a632eb8-3938-43a0-a7e1-xxxxxx \
					-Dfs.us3.endpoint=internal-cn-bj.ufileos.com \
					-Dfs.us3.async.wio.use=false \
					-Dfs.us3.async.wio.parallel=2 \
					-Dfs.us3.metadata.use=true \
					-Dmapreduce.job.user.classpath.first=true \
					-Dmapreduce.task.classpath.user.precedence=true \
					-libjars ./us3-bigdata-adaptor-2.8.5-1.1.0.jar \
					-workspace /workspace/us3distcptest /var/log/hadoop-yarn/apps/root/logs/application_1613870792268_0067 us3://bigdata-us3/hdfs_backup
21/03/03 16:28:54 INFO distcp.DistCp: Prepare to enter data, on workspace:/workspace/us3distcptest
21/03/03 16:28:54 INFO distcp.DistCp: Prepare to enter data by sources:
21/03/03 16:28:54 INFO distcp.DistCp: - /var/log/hadoop-yarn/apps/root/logs/application_1613870792268_0067
21/03/03 16:28:54 INFO distcp.DistCp: Prepare to enter data by target: us3://bigdata-us3/hdfs_backup
21/03/03 16:28:54 INFO distcp.CopyListing: check input file:/workspace/us3distcptest/input/input
21/03/03 16:28:55 INFO distcp.DistCp: us3 distcp need check size/count is 10
21/03/03 16:28:55 INFO distcp.DistCp: do check job, num of maps:1
21/03/03 16:28:55 INFO distcp.DistCpInputFormat: the size of files count by DistCp, size is 10
21/03/03 16:28:55 INFO distcp.DistCpInputFormat: Average size of map: 10, Number of maps:1, total size:10
21/03/03 16:28:56 INFO mapreduce.JobSubmitter: number of splits:1
21/03/03 16:28:56 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1614496134003_0032
21/03/03 16:28:56 INFO impl.YarnClientImpl: Submitted application application_1614496134003_0032
21/03/03 16:28:56 INFO mapreduce.Job: The url to track the job: http://master2:23188/proxy/application_1614496134003_0032/
21/03/03 16:28:56 INFO mapreduce.Job: Running job: job_1614496134003_0032
21/03/03 16:29:02 INFO mapreduce.Job: Job job_1614496134003_0032 running in uber mode : false
21/03/03 16:29:02 INFO mapreduce.Job:  map 0% reduce 0%
21/03/03 16:29:07 INFO mapreduce.Job:  map 100% reduce 0%
21/03/03 16:29:14 INFO mapreduce.Job:  map 100% reduce 100%
21/03/03 16:29:14 INFO mapreduce.Job: Job job_1614496134003_0032 completed successfully
21/03/03 16:29:14 INFO mapreduce.Job: Counters: 56
	File System Counters
		FILE: Number of bytes read=0
		FILE: Number of bytes written=350391
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=1853
		HDFS: Number of bytes written=1867
		HDFS: Number of read operations=17
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=2
		US3: Number of bytes read=0
		US3: Number of bytes written=0
		US3: Number of read operations=0
		US3: Number of large read operations=0
		US3: Number of write operations=0
	Job Counters
		Launched map tasks=1
		Launched reduce tasks=1
		Other local map tasks=1
		Total time spent by all maps in occupied slots (ms)=193914
		Total time spent by all reduces in occupied slots (ms)=329522
		Total time spent by all map tasks (ms)=3078
		Total time spent by all reduce tasks (ms)=2701
		Total vcore-milliseconds taken by all map tasks=3078
		Total vcore-milliseconds taken by all reduce tasks=2701
		Total megabyte-milliseconds taken by all map tasks=6192936
		Total megabyte-milliseconds taken by all reduce tasks=10544704
	Map-Reduce Framework
		Map input records=10
		Map output records=10
		Map output bytes=1502
		Map output materialized bytes=1528
		Input split bytes=116
		Combine input records=0
		Combine output records=0
		Reduce input groups=10
		Reduce shuffle bytes=1528
		Reduce input records=10
		Reduce output records=10
		Spilled Records=10
		Shuffled Maps =1
		Failed Shuffles=0
		Merged Map outputs=1
		GC time elapsed (ms)=190
		CPU time spent (ms)=1700
		Physical memory (bytes) snapshot=1480015872
		Virtual memory (bytes) snapshot=8427098112
		Total committed heap usage (bytes)=2974810112
	Shuffle Errors
		BAD_ID=0
		CONNECTION=0
		IO_ERROR=0
		WRONG_LENGTH=0
		WRONG_MAP=0
		WRONG_REDUCE=0
	cn.ucloud.us3.fs.distcp.DistCpMapper$US3_DISTCP_CHECK
		NUMBER_OF_DST_NOT_FOUND=10
		TOTAL_NUMBER_OF_INPUT_FILES=10
	File Input Format Counters
		Bytes Read=1737
	File Output Format Counters
		Bytes Written=1867
21/03/03 16:29:14 INFO distcp.DistCp: us3 distcp check taken: 0 Days 0 Hours 0 Minutes 11 Seconds 893 Milliseconds
21/03/03 16:29:14 WARN distcp.CopyListing: hdfs://Ucluster/workspace/us3distcptest/checkout/_SUCCESS read err:hdfs://Ucluster/workspace/us3distcptest/checkout/_SUCCESS not a SequenceFile
21/03/03 16:29:14 ERROR distcp.DistCp: us3 distcp, some files failed the integrity check, need to copy ~ o(╥﹏╥)o
21/03/03 16:29:14 WARN distcp.CopyListing: hdfs://Ucluster/workspace/us3distcptest/checkout/_SUCCESS read err:hdfs://Ucluster/workspace/us3distcptest/checkout/_SUCCESS not a SequenceFile
21/03/03 16:29:14 INFO distcp.DistCp: us3 distcp need copy size is 5063546
21/03/03 16:29:14 INFO distcp.DistCp: calculate map num, total size:5063546 ,size_per_task:4294967296
21/03/03 16:29:14 INFO distcp.DistCp: calculate map num:1
21/03/03 16:29:14 INFO distcp.DistCp: do copy job, num of maps:1
21/03/03 16:29:15 INFO distcp.DistCpInputFormat: the size of files count by DistCp, size is 5063546
21/03/03 16:29:15 INFO distcp.DistCpInputFormat: Average size of map: 5063546, Number of maps:1, total size:5063546
21/03/03 16:29:15 WARN distcp.DistCpInputFormat: hdfs://Ucluster/workspace/us3distcptest/checkout/_SUCCESS read err:hdfs://Ucluster/workspace/us3distcptest/checkout/_SUCCESS not a SequenceFile
21/03/03 16:29:15 INFO mapreduce.JobSubmitter: number of splits:1
21/03/03 16:29:15 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1614496134003_0033
21/03/03 16:29:15 INFO impl.YarnClientImpl: Submitted application application_1614496134003_0033
21/03/03 16:29:15 INFO mapreduce.Job: The url to track the job: http://master2:23188/proxy/application_1614496134003_0033/
21/03/03 16:29:15 INFO mapreduce.Job: Running job: job_1614496134003_0033
21/03/03 16:29:23 INFO mapreduce.Job: Job job_1614496134003_0033 running in uber mode : false
21/03/03 16:29:23 INFO mapreduce.Job:  map 0% reduce 0%
21/03/03 16:29:30 INFO mapreduce.Job:  map 100% reduce 0%
21/03/03 16:29:34 INFO mapreduce.Job:  map 100% reduce 100%
21/03/03 16:29:35 INFO mapreduce.Job: Job job_1614496134003_0033 completed successfully
21/03/03 16:29:35 INFO mapreduce.Job: Counters: 57
	File System Counters
		FILE: Number of bytes read=0
		FILE: Number of bytes written=348845
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=5065539
		HDFS: Number of bytes written=85
		HDFS: Number of read operations=27
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=2
		US3: Number of bytes read=0
		US3: Number of bytes written=5063546
		US3: Number of read operations=0
		US3: Number of large read operations=0
		US3: Number of write operations=0
	Job Counters
		Launched map tasks=1
		Launched reduce tasks=1
		Other local map tasks=1
		Total time spent by all maps in occupied slots (ms)=298179
		Total time spent by all reduces in occupied slots (ms)=321714
		Total time spent by all map tasks (ms)=4733
		Total time spent by all reduce tasks (ms)=2637
		Total vcore-milliseconds taken by all map tasks=4733
		Total vcore-milliseconds taken by all reduce tasks=2637
		Total megabyte-milliseconds taken by all map tasks=9522796
		Total megabyte-milliseconds taken by all reduce tasks=10294848
	Map-Reduce Framework
		Map input records=10
		Map output records=0
		Map output bytes=0
		Map output materialized bytes=6
		Input split bytes=126
		Combine input records=0
		Combine output records=0
		Reduce input groups=0
		Reduce shuffle bytes=6
		Reduce input records=0
		Reduce output records=0
		Spilled Records=0
		Shuffled Maps =1
		Failed Shuffles=0
		Merged Map outputs=1
		GC time elapsed (ms)=148
		CPU time spent (ms)=2660
		Physical memory (bytes) snapshot=1519132672
		Virtual memory (bytes) snapshot=8458067968
		Total committed heap usage (bytes)=3032481792
	Shuffle Errors
		BAD_ID=0
		CONNECTION=0
		IO_ERROR=0
		WRONG_LENGTH=0
		WRONG_MAP=0
		WRONG_REDUCE=0
	cn.ucloud.us3.fs.distcp.DistCpMapper$US3_DISTCP_COPY
		READ_SIZE_OF_SOURCE=5063546
		TOTAL_NUMBER_OF_INPUT_FILES=10
		WRITE_SIZE_OF_DISTINATION=5063546
	File Input Format Counters
		Bytes Read=1867
	File Output Format Counters
		Bytes Written=85
21/03/03 16:29:35 INFO distcp.DistCp: us3 distcp copy taken: 0 Days 0 Hours 0 Minutes 11 Seconds 872 Milliseconds
21/03/03 16:29:35 WARN distcp.CopyListing: hdfs://Ucluster/workspace/us3distcptest/cpout/_SUCCESS read err:hdfs://Ucluster/workspace/us3distcptest/cpout/_SUCCESS not a SequenceFile
21/03/03 16:29:35 INFO distcp.DistCp: us3 distcp check & copy succ!!
```

可以看到任务`application_1614496134003_0032`即为检查阶段，由于没有指定校验策略，所以默认只采用了`目的是否存在`和`长度是否相等`的策略，可以看到统计指标`cn.ucloud.us3.fs.distcp.DistCpMapper$US3_DISTCP_CHECK`的两项：

- TOTAL_NUMBER_OF_INPUT_FILES=10：表明待校验的有10个文件。
- NUMBER_OF_DST_NOT_FOUND=10：表明这10个文件在目的端都不存在

然后会自动对10个文件进行同步，任务`application_1614496134003_0033`则负责同步，可以看到最后同步已经成功。

## 使用校验

对上面的同步进行校验，校验不通过的自动同步。这里示例除了采用默认的`目的是否存在`，`长度是否相等` 策略，还增加了`checksum校验` 策略，其校验算法采用 `md5`。

```shell
➜  bin  ✗ hadoop jar ./us3-bigdata-adaptor-2.8.5-1.1.0.jar \
					cn.ucloud.us3.fs.distcp.DistCp \
					-Dfs.us3.impl=cn.ucloud.us3.fs.US3FileSystem \
					-Dfs.us3.access.key=TOKEN_7468973e-d192-4378-8253-xxxxxx \
					-Dfs.us3.secret.key=9a632eb8-3938-43a0-a7e1-xxxxxx \
					-Dfs.us3.endpoint=internal-cn-bj.ufileos.com \
					-Dfs.us3.async.wio.use=false \
					-Dfs.us3.async.wio.parallel=2 \
					-Dfs.us3.metadata.use=true \
					-Dmapreduce.job.user.classpath.first=true \
					-Dmapreduce.task.classpath.user.precedence=true \
					-libjars ./us3-bigdata-adaptor-2.8.5-1.1.0.jar \
					-workspace /workspace/us3distcptest -checksum all -algorithm md5 -enforce /var/log/hadoop-yarn/apps/root/logs/application_1613870792268_0067 us3://bigdata-us3/hdfs_backup
21/03/03 16:34:53 INFO distcp.DistCpOptions: set check sum mode:all
21/03/03 16:34:53 INFO distcp.DistCpOptions: set check sum alogrithm:md5
21/03/03 16:34:54 INFO distcp.DistCp: =====================================================================================
21/03/03 16:34:54 INFO distcp.DistCp: The last task has not been processed yet, on workspace:/workspace/us3distcptest
21/03/03 16:34:54 INFO distcp.DistCp: Start processing the last task......
21/03/03 16:34:54 INFO distcp.DistCp: =====================================================================================
21/03/03 16:34:54 INFO distcp.DistCp: us3 distcp need check size/count is 5063546
21/03/03 16:34:54 INFO distcp.DistCp: us3 distcp, drop workspace:/workspace/us3distcptest/checkout
21/03/03 16:34:54 INFO distcp.DistCp: do check job, num of maps:1
21/03/03 16:34:54 INFO distcp.DistCpInputFormat: the size of files count by DistCp, size is 5063546
21/03/03 16:34:54 INFO distcp.DistCpInputFormat: Average size of map: 5063546, Number of maps:1, total size:5063546
21/03/03 16:34:54 INFO mapreduce.JobSubmitter: number of splits:1
21/03/03 16:34:54 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1614496134003_0034
21/03/03 16:34:54 INFO impl.YarnClientImpl: Submitted application application_1614496134003_0034
21/03/03 16:34:54 INFO mapreduce.Job: The url to track the job: http://master2:23188/proxy/application_1614496134003_0034/
21/03/03 16:34:54 INFO mapreduce.Job: Running job: job_1614496134003_0034
21/03/03 16:35:01 INFO mapreduce.Job: Job job_1614496134003_0034 running in uber mode : false
21/03/03 16:35:01 INFO mapreduce.Job:  map 0% reduce 0%
21/03/03 16:35:07 INFO mapreduce.Job:  map 100% reduce 0%
21/03/03 16:35:15 INFO mapreduce.Job:  map 100% reduce 100%
21/03/03 16:35:15 INFO mapreduce.Job: Job job_1614496134003_0034 completed successfully
21/03/03 16:35:15 INFO mapreduce.Job: Counters: 57
	File System Counters
		FILE: Number of bytes read=0
		FILE: Number of bytes written=348871
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=5065399
		HDFS: Number of bytes written=85
		HDFS: Number of read operations=27
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=2
		US3: Number of bytes read=0
		US3: Number of bytes written=0
		US3: Number of read operations=0
		US3: Number of large read operations=0
		US3: Number of write operations=0
	Job Counters
		Launched map tasks=1
		Launched reduce tasks=1
		Other local map tasks=1
		Total time spent by all maps in occupied slots (ms)=252819
		Total time spent by all reduces in occupied slots (ms)=469334
		Total time spent by all map tasks (ms)=4013
		Total time spent by all reduce tasks (ms)=3847
		Total vcore-milliseconds taken by all map tasks=4013
		Total vcore-milliseconds taken by all reduce tasks=3847
		Total megabyte-milliseconds taken by all map tasks=8074156
		Total megabyte-milliseconds taken by all reduce tasks=15018688
	Map-Reduce Framework
		Map input records=10
		Map output records=0
		Map output bytes=0
		Map output materialized bytes=6
		Input split bytes=116
		Combine input records=0
		Combine output records=0
		Reduce input groups=0
		Reduce shuffle bytes=6
		Reduce input records=0
		Reduce output records=0
		Spilled Records=0
		Shuffled Maps =1
		Failed Shuffles=0
		Merged Map outputs=1
		GC time elapsed (ms)=170
		CPU time spent (ms)=2570
		Physical memory (bytes) snapshot=1501753344
		Virtual memory (bytes) snapshot=8458620928
		Total committed heap usage (bytes)=3024093184
	Shuffle Errors
		BAD_ID=0
		CONNECTION=0
		IO_ERROR=0
		WRONG_LENGTH=0
		WRONG_MAP=0
		WRONG_REDUCE=0
	cn.ucloud.us3.fs.distcp.DistCpMapper$US3_DISTCP_CHECK
		SIZE_OF_DISTINATION_CHECKSUM_READ=5063546
		SIZE_OF_SOURCE_CHECKSUM_READ=5063546
		TOTAL_NUMBER_OF_INPUT_FILES=10
	File Input Format Counters
		Bytes Read=1737
	File Output Format Counters
		Bytes Written=85
21/03/03 16:35:15 INFO distcp.DistCp: us3 distcp check taken: 0 Days 0 Hours 0 Minutes 13 Seconds 9 Milliseconds
21/03/03 16:35:15 WARN distcp.CopyListing: hdfs://Ucluster/workspace/us3distcptest/checkout/_SUCCESS read err:hdfs://Ucluster/workspace/us3distcptest/checkout/_SUCCESS not a SequenceFile
21/03/03 16:35:15 INFO distcp.DistCp: us3 distcp, all file integrity verification passed, no need to copy ~ ^_^
```

最后提示校验通过。

## 增量迁移

需要先删除`workspace`指定目录，然后按照前面***使用同步***的方法进行

## 缺陷

目前的缺陷有以下几点：

- 同步文件不能就近访问。不能把待同步的hdfs上相同节点的文件归并到指定任务中，并让任务分配到对应的hdfs节点。
- 不支持正则，通配等过滤规则。
- 不能对单个任务限流。但目前同步写入速度稳定在30MiB/s左右。
- 全量checksum校验会导致大量流量穿越。后续可以通过支持US3的Etag校验，把流量限定在hdfs集群内部。
- 任务分配算法不够均匀。
