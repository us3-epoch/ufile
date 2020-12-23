# US3FS 文件挂载工具

- [概述](#概述)
- [运行环境](#运行环境)
- [下载链接](#下载链接)
- [主要功能](#主要功能)
- [使用限制](#使用限制)
- [配置账号访问信息](#配置账号访问信息)
- [使用方式](#使用方式)
  - [配置访问权限](#配置访问权限)
  - [设置挂载只读](#设置挂载只读)
  - [开启日志](#开启日志)
  - [版本更新](#版本更新)
  - [帮助](#帮助)
- [常用选项](#常用选项)
  - [性能相关](#性能相关)
- [选项列表](#选项列表)
- [使用示例](#使用示例)

## 概述

US3FS 是一款在 Linux 环境中提供 US3 的存储空间（bucket）挂载到本地挂载点的工具。挂载成功后，您可以像操作本地文件一样操作存储空间中的文件。US3FS 提供的共享文件系统访问解决方案不仅可以实现对对象存储进行随机读写，而且还能获得对象存储按需使用，容量无上限的能力。

## 运行环境

us3fs 基于用户态 fuse 实现，您的机器需要支持 fuse。

建议您将 us3fs 运行在以下环境中：

* linux
  * ceontos 7.0及以上 (可通过`cat /etc/redhat-release`查看)
  * ubuntu 16.04及以上 (可通过`cat /etc/issue`查看)

* 网络环境：us3fs 支持在 UCloud 内网以及互联网环境下使用，在内网环境下，你可以使用内网域名以提升性能和稳定性。

## 下载链接

```
curl -o us3fs http://ufile-release.cn-bj.ufileos.com/us3fs%2Fus3fs
```

## 主要功能

* 支持POSIX文件系统的大部分功能，如读，顺序写；权限；uid/gid。
* 使用US3的分片上传功能上传大文件。
* 支持etag和MD5校验，保证数据一致性。

## 使用限制

* 不支持读取归档类型的文件
* 不支持随机写/追加写
* 只支持文件之间的 rename
* 不支持硬/软链接
* 多个客户端挂载同一个US3存储空间时，需要用户自行维护数据一致性

-----

## 配置账号访问信息

编辑/etc/us3fs/us3fs.conf并增加如下信息(如果没有该目录需要自行创建):

```yaml
bucket: your_bucket
access_key: ***********************************
secret_key: ***********************************
endpoint: ufile.cn-north-02.ucloud.cn（your_endpoint）
```
*冒号后面有单个空格*

* **bucket**: 桶名，需要和挂载的桶名一致
* **access_key**: 公钥，支持token秘钥和api秘钥两种模式
* **secret_key**: 私钥，支持token秘钥和api秘钥两种模式
* **endpoint**: 访问域名，详见[地域和域名](https://docs.ucloud.cn/ufile/introduction/region)。填写时需要去掉`www.`

当需要在一台机器上挂载多个存储空间时，可以通过`--passwd=passwd_file`指定账号信息。

下载us3fs后。使用`chmod +x us3fs`增加可执行权限，如果需要直接执行，可将us3fs移动到/bin目录下。示例：

```bash
chmod +x us3fs
./us3fs --passwd=passwd_file <bucket> <mountpoint>

mv us3fs /bin/us3fs
us3fs --passwd=passwd_file <bucket> <mountpoint>
```

## 使用方式

* 挂载

```
us3fs [global options] <bucket> <mountpoint>
```

* 卸载

```
umount <mountpoint>
```

-----

### 配置访问权限

us3fs 挂载的默认访问权限为当前挂载用户，如果需要允许其他用户/用户组访问挂载点，可以使用如下参数：

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

在`/etc/fuse.conf`中增加`user_allow_other`

### 设置挂载只读

挂载时时指定`-o ro`。

### 开启日志

* --debug_u 开启us3fs日志
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

-----

## 帮助

通过`us3fs -h`查看us3fs支持的参数

```bash
❯ us3fs -h
us3fs - a single posix file system based on ufile
USAGE
  us3fs [global options] bucket mountpoint
FUSE
  --entry_timeout value   How long to cache dentry for inode for fuse. (default: 0s)
  --attr_timeout value    How long to cache inode attr for fuse (default: 0s)
  --dcache_timeout value  How long to cache dentry for us3fs (default: 0s)
  --async_read            Perform all reads (even read-ahead) asynchronously
  --sync_read             Perform all reads (even read-ahead) synchronously
  -o value                specify fuse option
  
OS
  --retry value      number of times to retry a failed I/O (default: 5)
  --parallel value   number of parallel I/O thread (default: 20)
  --debug_fuse       set debug level for fuse
  --debug_u          set debug level for u
  --readahead value  readahead size, (MB) (default: 16)
  --critical         Check every part's etag, this option will cost cpu
  --passwd value     specify access file (default: "/etc/us3fs/us3fs.conf")
  --enable_md5       enalbe md5 in http header
  --uid value        specify default uid (default: 0)
  --gid value        specify default gid (default: 0)
  
MISC
  --help, -h  show help
  -f          foreground
```

## 常用选项

* fuse设置
us3fs基于fuse实现，所以除了us3fs自身的设置外，还支持fuse的设置，格式如下：

```bash
-o option=value
```

### 性能相关

* `parallel`：设置并发线程，对cpu负载有一定影响。建议设置在20~40较为合理
* `critical`：写入文件时启用本地etag校验，相比未开启会提高约50%的cpu占用。
* `readahead`：预读窗口大小，由于fuse自身有读写窗口的限制，一定的预读大小对读取性能有显著提升。建议设置在16~32

## 选项列表

| 选项名称       | 描述                                            |
| -------------- | ----------------------------------------------- |
| entry_timeout  | 指定fuse缓存被查找的文件名的时间，默认为0s    |
| attr_timeout   | 指定fuse缓存文件/目录属性的时间，默认为0s     |
| dcache_timeout | 指定us3fs缓存文件/目录属性的时间，默认为0s    |
| async_read     | 指定fuse读取为异步模式，默认开启                |
| sync_read      | 指定fuse读取为同步模式(包括预读)，默认关闭      |
| retry          | 请求失败后重试次数，默认5次                   |
| parallel       | I/O并发线程数，默认20个                       |
| debug_fuse     | 指定用户态fuse日志级别为debuy，默认关闭       |
| debug_u        | 指定us3fs日志级别为debug，默认为Info级别      |
| readahead      | 指定预读窗口最大值(单位MB)，默认为16          |
| critical       | 写入文件时校验每个分片的etag，默认关闭        |
| passwd         | 指定账户文件，默认路径`/etc/us3fs/us3fs.conf` |
| enable_md5     | 在http请求头中增加md5校验，默认关闭           |
| uid            | 指定文件所属的默认用户，默认当前用户          |
| gid            | 指定文件所属的默认用户组，默认当前用户组      |
| help, h        | 查看帮助                                        |
| f              | 挂载时启用前台模式                              |
| update         | 更新us3fs版本，新版本路径为/bin/us3fs           |

* fuse常用选项列表

| 选项名称    | 描述                                     |
| ----------- | ---------------------------------------- |
| allow_other | 指定文件系统可以所有用户访问<br>默认关闭 |
| ro          | 指定当前文件系统为只读                   |

## 使用示例

* **entry_timeout**, **attr_timeout**, **dcache_timeout**:

设置`dcache_timeout`可增加文件/目录属性在内存中的有效时间，增强使用体验。建议`entry_timeout` , `attr_timeout`设置时间小于`dcache_timeout`

*注：开启缓存后，可能造成用户读取目录的内容和实际bucket中的内容不一致。*

示例：ls包含10000个文件的目录耗时

```
[root@10-9-120-211 ~]# us3fs --dcache_timeout=60s --entry_timeout=60s --attr_timeout=60s testzwb /data/u2fs
[root@10-9-120-211 ~]# time ls -la /data/u2fs/test | wc -l
10003

real    0m5.964s
user    0m0.033s
sys     0m0.232s
[root@10-9-120-211 ~]# 
[root@10-9-120-211 ~]# 
[root@10-9-120-211 ~]# time ls -la /data/u2fs/test | wc -l
10003

real    0m0.872s
user    0m0.029s
sys     0m0.133s
```

* **async_read**, **sync_read**

默认读取模式为异步，同步读取性能较差。

示例如下：

```
[root@10-9-120-211 ~]#  us3fs --sync_read  testzwb /data/u2fs
[root@10-9-120-211 ~]# dd if=/data/u2fs/testbig of=/dev/null bs=4M count=10
10+0 records in
10+0 records out
41943040 bytes (42 MB, 40 MiB) copied, 10.2345 s, 4.1 MB/s

[root@10-9-120-211 ~]#  us3fs --async_read  testzwb /data/u2fs
[root@10-9-120-211 ~]# dd if=/data/u2fs/testbig of=/dev/null bs=4M count=10
10+0 records in
10+0 records out
41943040 bytes (42 MB, 40 MiB) copied, 0.685801 s, 61.2 MB/s
```

* **parallel**

增大并发数可提升读写性能，相应的也行增加系统资源占用。

示例如下:

```
// 默认并发数20
[root@10-9-120-211 ~]# us3fs testzwb /data/u2fs/
[root@10-9-120-211 ~]# dd if=/dev/zero of=/data/u2fs/testbig bs=4M count=1024
1024+0 records in
1024+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 25.5351 s, 168 MB/s

// 调整并发数为32
[root@10-9-120-211 ~]# us3fs --parallel=32 testzwb /data/u2fs/
[root@10-9-120-211 ~]# dd if=/dev/zero of=/data/u2fs/testbig bs=4M count=1024
1024+0 records in
1024+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 18.3614 s, 234 MB/s
```

* **readahead**

调整预读窗口大小对大文件的顺序读有较大影响，建议在16~32MB.

示例如下：

```
// 默认预读大小16MB
[root@10-9-120-211 ~]# dd if=/data/u2fs/testbig of=/dev/null bs=4M count=1024
1024+0 records in
1024+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 60.0498 s, 71.5 MB/s

// 调整预读大小为32MB
[root@10-9-120-211 ~]# us3fs --readahead=32 testzwb /data/u2fs/
[root@10-9-120-211 ~]# dd if=/data/u2fs/testbig of=/dev/null bs=4M count=1024
1024+0 records in
1024+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 37.6013 s, 114 MB/s
```
