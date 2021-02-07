# 快速上手

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

us3fs挂载的默认访问权限为当前挂载用户，如果需要允许其他用户/用户组访问挂载点，可以使用如下参数：

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

### 设置挂载只读

挂载时时指定`-o ro`。

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

-----

## 使用帮助

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
  --wb                    write-back

OS
  --retry value      number of times to retry a failed I/O (default: 5)
  --parallel value   number of parallel I/O thread (default: 20)
  --debug_fuse       set debug level for fuse
  --readahead value  readahead size, (MB) (default: 16)
  --critical         Check every part's etag, this option will cost cpu
  --passwd value     specify access file (default: "/etc/us3fs/us3fs.conf")
  --enable_md5       enalbe md5 in http header
  --uid value        specify default uid (default: 0)
  --gid value        specify default gid (default: 0)
  --nodirbl          check empty dir
  --update           update us3fs to /bin/us3fs
  -n                 doesn't check access when mount us3fs
  -l                 use local cache
  -s value           small file size (default: 10485760)
  -p value           specify local cache location (default: "/tmp/us3fs")

MISC
  --help, -h  show help
  -f          foreground

```

## 选项列表

| 选项名称       | 描述                                                                                |
| -------------- | ----------------------------------------------------------------------------------- |
| entry_timeout  | 指定fuse缓存被查找的文件名的时间<br>默认为0s                                        |
| attr_timeout   | 指定fuse缓存文件/目录属性的时间<br>默认为0s                                         |
| dcache_timeout | 指定us3fs缓存文件/目录属性的时间<br>默认为0s                                        |
| async_read     | 指定fuse读取为异步模式。默认开启                                                    |
| sync_read      | 指定fuse读取为同步模式(包括预读)。默认关闭                                          |
| retry          | 请求失败后重试次数<br>默认5次                                                       |
| parallel       | I/O并发线程数<br>默认20个                                                           |
| debug_fuse     | 指定用户态fuse日志级别为debuy<br>默认关闭                                           |
| level          | 指定us3fs日志级别<br>默认为Info级别                                                 |
| readahead      | 指定预读窗口最大值(单位MB)<br>默认为16                                              |
| critical       | 写入文件时校验每个分片的etag<br>默认关闭                                            |
| passwd         | 指定账户文件<br>默认路径`/etc/us3fs/us3fs.conf`                                     |
| enable_md5     | 在http请求头中增加md5校验<br>默认关闭                                               |
| uid            | 指定文件所属的默认用户<br>默认当前用户                                              |
| gid            | 指定文件所属的默认用户组<br>默认当前用户组                                          |
| help, h        | 查看帮助                                                                            |
| f              | 挂载时启用前台模式，相关输出会打印到标准输出                                        |
| update         | 更新us3fs版本，新版本路径为/bin/us3fs                                               |
| wb             | 指定写入使用writeback方式。不支持覆盖写/追加写                                      |
| nodirbl        | lookup时不检查没有对象的目录                                                        |
| n              | 挂载时不检查bucket权限，如果没有所在地域没有列表服务可开启                          |
| l              | 开启后对小文件使用本地目录做缓存，异步上传。具体使用示例见[小文件场景](#小文件场景) |
| s              | 指定异步上传小文件的最大大小，单位字节。最大10MB                                    |
| p              | 指定启用小文件异步上传使用的本地缓存目录                                            |

* fuse常用选项列表（与`-o`一起使用）

| 选项名称    | 描述                                     |
| ----------- | ---------------------------------------- |
| allow_other | 指定文件系统可以所有用户访问<br>默认关闭 |
| ro          | 指定当前文件系统为只读                   |

**使用方式**

```bash
-o option=value
```


## 使用示例

* **entry_timeout**, **attr_timeout**, **dcache_timeout**:

设置`dcache_timeout`可增加文件/目录属性在内存中的有效时间，增强使用体验。建议`entry_timeout` , `attr_timeout`设置时间小于`dcache_timeout`

*注：开启缓存后，可能造成用户读取目录的内容和实际bucket中的内容不一致。*

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

* **async_read**, **sync_read**

默认读取模式为异步，同步读取性能较差。

示例如下：

```
[root@10-9-120-211 ~]#  us3fs --sync_read  <your_bucket> <mountpoint>
[root@10-9-120-211 ~]# dd if=<your_file> of=/dev/null bs=4M count=10
10+0 records in
10+0 records out
41943040 bytes (42 MB, 40 MiB) copied, 10.2345 s, 4.1 MB/s

[root@10-9-120-211 ~]#  us3fs --async_read  <your_bucket> <mountpoint>
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

调整预读窗口大小对大文件的顺序读有较大影响，建议在16~32MB.

示例如下：

```
// 默认预读大小16MB
[root@10-9-120-211 ~]# dd if=<your_file> of=/dev/null bs=4M count=1024
1024+0 records in
1024+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 60.0498 s, 71.5 MB/s

// 调整预读大小为32MB
[root@10-9-120-211 ~]# us3fs --readahead=32 <your_bucket> <mountpoint>
[root@10-9-120-211 ~]# dd if=<your_file> of=/dev/null bs=4M count=1024
1024+0 records in
1024+0 records out
4294967296 bytes (4.3 GB, 4.0 GiB) copied, 37.6013 s, 114 MB/s
```

## 场景化参数设置

### 性能相关参数

* `parallel`：设置并发线程，对cpu负载有一定影响。建议设置在20~40较为合理
* `critical`：写入文件时启用本地etag校验，相比未开启会提高约50%的cpu占用。
* `readahead`：预读窗口大小，由于fuse自身有读写窗口的限制，一定的预读大小对读取性能有显著提升。建议设置在16~32

### 小文件场景

对于大量小文件场景，如果对性能有要求，可指定`-l`开启本地本地缓存。当启用本地缓存后，us3fs挂载后首先会将指定缓存目录下已存在的所有小于10MB的文件按照其路径上传到bucket中。当写入文件大小不大于设置的小文件大小（最大10MB），文件会尝试写入本地缓存目录，写入成功后即返回，后端异步上传到us3。写入失败（如权限不足，空间不足等）则仍然使用同步方式写入us3对象存储

*注：异步上传可能出现写入后端失败，us3fs会一直重试直到写入成功。*
