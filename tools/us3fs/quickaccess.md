# 快速上手

- [视频教学](#视频教学)
- [使用方式](#使用方式)
  - [配置访问权限](#配置访问权限)
  - [设置挂载只读](#设置挂载只读)
  - [开启日志](#开启日志)
  - [版本更新](#版本更新)
- [使用帮助](#使用帮助)
- [选项列表](#选项列表)
- [使用示例](#使用示例)
- [场景化参数设置](#场景化参数设置)
  - [性能相关参数](#性能相关参数)
  - [小文件场景](#小文件场景)
- [本地服务支持](#本地服务支持)

## 视频教学

观看以下视频，快速上手使用US3FS

<video id="video" length=1000 width=800 controls="" preload="none" poster="https://static.ucloud.cn/945f2902d9d0d5451607db50629b8dab.png">
      <source id="mp4" src="http://caozuozhinan.cn-bj.ufileos.com/录屏3 us3 fs.mp4">
      </video>

## 使用方式

* 挂载

```
us3fs [global options] <bucket> <mountpoint>
```
> 参数`<bucket>`和`<mountpoint>`必须依次作为最后两个参数，否则其他参数不能生效

* 卸载

```
umount <mountpoint>
```

> *windows 对cmd执行`Ctrl+C`* 

-----

### 配置访问权限

us3fs挂载的默认访问权限为当前挂载用户，如果需要允许其他用户/用户组访问挂载点，可以使用如下参数:

* `-o allow_other`：允许任何用户都可以访问文件。
* `--uid=xxx`：指定默认的用户
* `--gid=xxx`：指定默认的用户组
可通过`id`命令获取用户的uid/gid信息，示例如下：

```bash
// 在ubuntu账户下挂载默认用户和用户组为www的us3fs
ubuntu:~$ id www
uid=1001(www) gid=1001(www) groups=1001(www)

ubuntu:~$ us3fs --uid=1001 --gid=1001 -o allow_other <bucket> <mountpoint>
```

* 如果挂载出现以下问题

```bash
stderr:
/bin/fusermount: option allow_other only allowed if 'user_allow_other' is set in /etc/fuse.conf
```

在`/etc/fuse.conf`中增加`user_allow_other`。

> *windows下只支持`--uid`和`--gid`*

### 设置挂载只读

挂载时时指定`-o ro`。

> windows下不支持

### 开启日志

* --level=info/debug/error 开启指定级别的us3fs日志
* --debug_fuse 开启用户态fuse日志
  * centos
    日志在/var/log/messages
  * ubuntu
    日志在/var/log/syslog
* 挂载时指定-f，us3fs会以前台模式挂载，日志会输出到屏幕上。

### 版本更新

执行如下命令：

```
us3fs --update
```

更新后的可执行文件放在`/bin/`目录下

> *windows无效*

-----

## 使用帮助

通过`us3fs -h`查看us3fs支持的参数

#### Linux

```bash
❯ us3fs -h
us3fs - a single posix file system based on us3
USAGE
  us3fs [global options] bucket mountpoint
Version
  US3FS Version: 1.6.6
  Commit ID: 541a5aa
  Build: 2022-01-21:11:51:10
  Go Version: go1.16.3 linux/amd64

FUSE
  -o value                      Specify fuse/winfsp option
  --entry_timeout value         How long to cache dentry for inode for fuse. (default: 5m0s)
  --attr_timeout value          How long to cache inode attr for fuse (default: 5m0s)
  --disable_async_read          Disable all read (even read-ahead) operations asynchronously
  --wb                          Enable writeback mode, which is turned off by default
  --max_background value        Specify the max_background parameter of fuse kernel(>=7.13), currently fuse usespace supports up to 1024 (default: 64)
  --congestion_threshold value  Specify the congestion_threshold parameter of fuse kernel(>=7.13), currently fuse usespace supports up to 768 (default: 48)
  --async_dio                   Enable the async_dio parameter of fuse kernel, async_dio is disabled by default
  --keep_pagecache              Turn on pagecache, when the file is opened, it will be decided whether to update according to the modification time of the inode, so please pay attention to the attr_timeout and dcache_timeout parameters will have a certain impact on this

OS
  --dcache_timeout value  How long to cache dentry for us3fs (default: 5m0s)
  --retry value           Number of times to retry a failed I/O (default: 5)
  --parallel value        Number of parallel I/O thread (default: 32)
  --debug                 Set debug level for fuse/winfsp
  --level value           Set log level: error/warn/info/debug (default: "info")
  --readahead value       Readahead size. e.g.: 1m/1k/1  (default: "0")
  --etag value            Check etag for part. value is percent(0~100) (default: 50)
  --passwd value          specify access file (default: "/etc/us3fs/us3fs.yaml")
  --enable_md5            Enalbe md5 in http header
  --uid value             Specify default uid (default: 1000)
  --gid value             Specify default gid (default: 1000)
  --disable_check_vdir    disable detection of virtual directories
  --update                Update us3fs to /bin/us3fs
  -n                      Doesn't check access when mount us3fs
  -l                      Enable local cache for small file
  -p value                Specify local cache location (default: "/tmp/us3fs/")
  --prefix value          Specify bucket prefix path
  --gfl                   Enable get_file_list
  --direct_read           Enable cache bypass read
  --perf_dump value       How long to output the performance dump (default: 1h0m0s)
  --skip_ne_dir_lookup    Skip non-essential directory checking, such as files ending in ".log",".png",".jpg", etc.
  --storage_class value   Storage type, including "STANDARD", "IA" (default: "STANDARD")

MISC
  --help, -h  show help
  -f          foreground
```

#### Windows

```shell
us3fs - a single posix file system based on us3
USAGE
  us3fs [global options] bucket mountpoint
Version
  US3FS Version: 1.6.6
  Commit ID: 541a5aa
  Build: 2022-01-21:11:51:10
  Go Version: go1.16.3 linux/amd64

WinFSP
  -o value                     Specify fuse/winfsp option
  --dir_info_timeout value     The expiration time of the directory information, in seconds (default: 5)
  --file_info_timeout value    File information expiration time, in seconds (default: 5)
  --volume_info_timeout value  Volume information expiration time, in seconds (default: 5)
  --case_insensitive           Is case sensitive
  --keep_filecache             keep filecache

OS
  --dcache_timeout value  How long to cache dentry for us3fs (default: 5m0s)
  --retry value           Number of times to retry a failed I/O (default: 5)
  --parallel value        Number of parallel I/O thread (default: 32)
  --debug                 Set debug level for fuse/winfsp
  --level value           Set log level: error/warn/info/debug (default: "info")
  --readahead value       Readahead size. e.g.: 1m/1k/1  (default: "0")
  --etag value            Check etag for part. value is percent(0~100) (default: 50)
  --passwd value          specify access file (default: "/etc/us3fs/us3fs.conf")
  --enable_md5            Enalbe md5 in http header
  --uid value             Specify default uid (default: -1)
  --gid value             Specify default gid (default: -1)
  --disable_check_vdir    disable detection of virtual directories
  --update                Update us3fs to /bin/us3fs
  -n                      Doesn't check access when mount us3fs
  -l                      Enable local cache for small file
  -p value                Specify local cache location (default: "/tmp/us3fs/")
  --prefix value          Specify bucket prefix path
  --gfl                   Enable get_file_list
  --direct_read           Enable cache bypass read
  --perf_dump value       How long to output the performance dump (default: 1h0m0s)
  --skip_ne_dir_lookup    Skip non-essential directory checking, such as files ending in ".log",".png",".jpg", etc.
  --storage_class value   Storage type, including "STANDARD", "IA" (default: "STANDARD")

MISC
  --help, -h  show help
  -f          foreground
```

> *Linux和Windows的参数区别主要体现在`FUSE`和`WinFsp`*

## 选项列表

#### WinFsp

| 选项名称            | 描述                       |
| ------------------- | -------------------------- |
| o                   | WinFsp支持的option参数     |
| dir_info_timeout    | 目录缓存的超时时间，默认5s |
| file_info_timeout   | 文件缓存的超时时间，默认5s |
| volume_info_timeout | 卷信息的超时时间，默认5s   |
| keep_filecache      | 是否把文件放入缓存         |
|                     |                            |

#### FUSE

| 选项名称             | 描述                                                         |
| -------------------- | ------------------------------------------------------------ |
| o                    | FUSE支持的option参数                                         |
| entry_timeout        | 指定fuse缓存被查找的文件名的时间<br/>默认为5min              |
| attr_timeout         | 指定fuse缓存文件/目录属性的时间<br/>默认为5min               |
| disable_async_read   | 关闭fuse kernel预读使用异步模式。默认开启                    |
| wb                   | 指定写入使用writeback方式。不支持覆盖写/追加写               |
| max_background       | 指定fuse kernel的max_background参数(fuse kernel版本>=7.13)，<br>目前fuse usespace最多支持1024(默认：64)，该参数能提升direct io的并行度 |
| congestion_threshold | 指定fuse kernel(fuse kernel版本>=7.13)的congestion_threshold参<br>数，目前fuse usespace最多支持768（默认：48），该参数会触发并行IO的拥塞控制 |
| async_dio            | 开启fuse内核的async_dio参数，默认关闭async_dio。该参数开启后，<br>fuse kernel对direct io进行异步处理 |
| keep_pagecache       | 开启pagecache，文件打开时会根据inode的修改时间以及大小变化决<br>定是否更新，所以请注意entry_timeout和dcache_timeout参数对此会<br>有一定影响，使得未能及时感知到文件修改时间、大小变化 |

* fuse常用选项列表（与`-o`一起使用）

| 选项名称    | 描述                                     |
| ----------- | ---------------------------------------- |
| allow_other | 指定文件系统可以所有用户访问<br>默认关闭 |
| ro          | 指定当前文件系统为只读                   |

**使用方式**

```bash
-o option=value
```

#### OS(Object Storage)

| 选项名称           | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| dcache_timeout     | dentry cache在us3fs中的缓存时长<br/>默认为5min               |
| retry              | 请求失败后重试次数，默认5次                                  |
| parallel           | I/O并发线程数<br>默认20个                                    |
| debug              | 指定用户态fuse日志级别为debuy<br>默认关闭                    |
| level              | 指定us3fs日志级别<br>默认为Info级别                          |
| readahead          | 预读大小。 例如：1m/1k/1（默认：“0”）                        |
| etag               | 检查上传数据的 etag所占百分比。 值是百分比（0~100）（默认值：50） |
| passwd             | 指定账户文件，默认路径`/etc/us3fs/us3fs.yaml`  // *windows 平台自定义* |
| enable_md5         | 在http请求头中增加md5校验，默认关闭                          |
| uid                | 指定文件所属的默认用户，默认当前用户                         |
| gid                | 指定文件所属的默认用户组，默认当前用户组                     |
| disable_check_vdir | 禁用虚拟目录检测                                             |
| update             | 更新us3fs版本，新版本路径为/bin/us3fs                        |
| n                  | 挂载时不检查bucket权限，如果没有所在地域没有列表服务可开启   |
| l                  | 开启后对小文件使用本地目录做缓存，异步上传。具体使用示例见[小文件场景](#小文件场景) |
| p                  | 指定小文件异步上传的本地缓存目录                             |
| prefix             | 指定挂载的bucket前缀目录，默认为空                           |
| gfl                | 对于没有ListObjects API支持的Endpoint, 该参数可以绕过,通过PrefixFileList API模拟 |
| direct_read        | 开启后，绕过us3fs内部缓存组织模块，直接读取us3数据，<br>对于被频繁访问的文件会有一定性能降低，反之有利于降低时延 |
| perf_dump          | 指定时间周期输出时延统计信息，默认周期是1hour                |
| skip_ne_dir_lookup | 跳过非必要的目录检查，目前过滤支持".jpe"、".jpeg"、".png"、<br>".gz"、".tgz"、".gz"、".tgz"、".log"、".plot"、".js"、".html"、<br>".css"、".apk"为后缀的文件，需要确保bucket下没有用以上后缀<br>作为目录后缀的情况 |
| storage_class      | 指定写入US3中文件的存储类型，支持`STANDARD`(标准), `IA`(低频)两种。 (default: `STANDARD`)|

#### MISC

| 选项名称 | 描述                                         |
| -------- | -------------------------------------------- |
| help, h  | 查看帮助                                     |
| f        | 挂载时启用前台模式，相关输出会打印到标准输出 |

> *windows下`-f`参数无效*

## 使用示例

* **entry_timeout**, **attr_timeout**, **dcache_timeout**:

设置`dcache_timeout`可增加文件/目录属性在内存中的有效时间，增强使用体验。建议`entry_timeout` , `attr_timeout`设置时间小于`dcache_timeout`

*注：开启缓存后，可能造成用户读取目录的内容和实际bucket中的内容不一致。默认为开启，需要关闭请设置为0s*

示例：ls包含10000个文件的目录耗时

```
[root@10-9-120-211 ~]# us3fs --dcache_timeout=60s --entry_timeout=60s --attr_timeout=60s <your_bucket> <mountpoint>
[root@10-9-120-211 ~]# time ls -la <your_dir> | wc -l
10003

real    0m5.964s
user    0m0.033s
sys     0m0.232s
[root@10-9-120-211 ~]# 
[root@10-9-120-211 ~]# 
[root@10-9-120-211 ~]# time ls -la <your_dir> | wc -l
10003

real    0m0.872s
user    0m0.029s
sys     0m0.133s
```

* **disable_async_read**

默认读取模式为异步，同步读取性能较差。

示例如下：

```
[root@10-9-120-211 ~]#  us3fs --disable_async_read <your_bucket> <mountpoint>
[root@10-9-120-211 ~]# dd if=<your_file> of=/dev/null bs=4M count=10
10+0 records in
10+0 records out
41943040 bytes (42 MB, 40 MiB) copied, 10.2345 s, 4.1 MB/s

[root@10-9-120-211 ~]#  us3fs <your_bucket> <mountpoint>
[root@10-9-120-211 ~]# dd if=<your_file> of=/dev/null bs=4M count=10
10+0 records in
10+0 records out
41943040 bytes (42 MB, 40 MiB) copied, 0.685801 s, 61.2 MB/s
```

* **parallel**

增大并发数可提升读写性能，相应的也行增加系统资源占用。

示例如下:

```
// 默认并发数20
[root@10-9-120-211 ~]# us3fs <your_bucket> <mountpoint>
[root@10-9-120-211 ~]# dd if=/dev/zero of=<your_file> bs=4M count=1024
1024+0 records in
1024+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 25.5351 s, 168 MB/s

// 调整并发数为32
[root@10-9-120-211 ~]# us3fs --parallel=32 <your_bucket> <mountpoint>
[root@10-9-120-211 ~]# dd if=/dev/zero of=<your_file> bs=4M count=1024
1024+0 records in
1024+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 18.3614 s, 234 MB/s
```

* **readahead**

调整预读窗口大小对大文件的顺序读有较大影响，建议在16m~32m，但会增加内存消耗，可以适当缩小预读窗口。

示例如下：

```shell
// 默认预读大小16MB
[root@10-9-120-211 ~]# dd if=<your_file> of=/dev/null bs=4M count=1024
1024+0 records in
1024+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 60.0498 s, 71.5 MB/s

// 调整预读大小为32MB
[root@10-9-120-211 ~]# us3fs --readahead=32m <your_bucket> <mountpoint>
[root@10-9-120-211 ~]# dd if=<your_file> of=/dev/null bs=4M count=1024
1024+0 records in
1024+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 37.6013 s, 114 MB/s
```

## 场景化参数设置

### 性能相关参数

* `parallel`：设置并发线程，对cpu负载有一定影响。建议设置在20~40较为合理
* `critical`：写入文件时启用本地etag校验，相比未开启会提高约50%的cpu占用。
* `readahead`：预读窗口大小，由于fuse自身有读写窗口的限制，一定的预读大小对读取性能有显著提升。建议设置在16m~32m，但会增加内存消耗，可以适当缩小预读窗口。
* `keep_pagecache`: 尽可能缓存数据内容在vfs pagecache中，直到文件的修改时间和大小发生变化，才无效掉pagecache中的历史数据。
* `wb`: 该参数会使得写入的IO会尽量在pagecache中合并，然后以大IO(默认128K)发送到us3fs。 

### 小文件场景

对于大量小文件场景，如果对性能有要求，可指定`-l`开启本地本地缓存。当启用本地缓存后，us3fs挂载后首先会将指定缓存目录下已存在的所有小于等于4MB的文件按照其路径上传到bucket中。当写入文件大小不大于4MB，文件会尝试写入本地缓存目录，写入成功后即返回，后端异步上传到us3。写入失败（如权限不足，空间不足等）则仍然使用同步方式写入us3对象存储

*注：异步上传可能出现写入后端失败,us3fs会一直重试直到写入成功。*

## 自动挂载

推荐的最佳实践方案是采用systemctl来保证服务异常退出，或者机器重启后能重新挂载us3fs，但需要注意Linux(Windows平台暂未提供方案)的发行版本是否支持systemctl：

- Ubuntu >= 15.04
- Centos、RHEL >= 7

满足系统要求后，经过如下几个简单步骤即可：

1. 设置配置

在`/etc/systemd/system/`目录下创建名为`us3fs.service`的文件，并增加如下内容

```
[Unit]
Description=US3FS (User Space FileSystem for US3)
Requires=network-online.target
After=network-online.target

[Service]
Type=forking
User=<user>
Group=<group>
Restart=always
RestartSec=10
ExecStart=/bin/us3fs --passwd=/etc/us3fs/us3fs.conf --keep_pagecache <your_bucket> <mountpoint> 
ExecStop=/bin/umount <monutpoint>
[Install]
WantedBy=multi-user.target
```

* *User*为需要访问挂载点的用户。如root。
* *Group*为需要访问挂载点的用户组。如root。
* *ExecStart*为挂载命令，按照需要自行填写

执行`systemctl daemon-reload`配置则会生效。

2. 服务启用或停止

   执行`systemctl start us3fs.service`启动服务；

   执行`systemctl stop us3fs.service`停止服务；

   执行`systemctl restart us3fs.service`重启服务；

   执行`systemctl status us3fs.service`查看服务状态;

3. 设置为开机自启动

   执行`systemctl enable us3fs.service`；
