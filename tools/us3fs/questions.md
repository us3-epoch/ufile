# 常见问题

## 挂载失败，日志出现“internal error”
所在地域不支持列表服务，可指定`--gfl`重新挂载。（*不支持ListObjects API地域*）

## 挂载时出现403

- 日志ErrMsg信息出现"action not allow"，则检查token相应权限，缺失并添加，过5min后测试
- 日志ErrMsg信息出现"invlid signature"，先检查配置文件字段是否符合官方文档说明，其次检查token公私钥是否粘贴完整

## 异常退出后再次挂载报错

1. 执行：umount <挂载点>，如果失败，进入2：
2. 执行：sudo umount <挂载点>，如果失败，联系技术支持

## 读写发生错误

报错信息如：”Input/Output error“，进行如下处理：

1. 检查系统日志：
   1. Centos的路径：/var/log/message
   2. Ubuntu的路径：/var/log/syslog
2. 如果通过日志发现是有http状态码为500错误，请联系技术支持查询该Bucket请求报500原因。

## 当前用户挂载后其他用户无法访问

添加参数`-o allow_other `

## 挂载后发现跟控制台文件信息不一致
该问题是由于默认开启entry_timeout,attr_timeout,dcache_timeout参数，并5min过期时间，如果对一致性敏感，建议这些参数设置为0s来关闭

## 降低读写时延的方法
- 应用层系统调用写入尽量采用大IO(>=128KiB，如果内存允许使用>=1MiB的提升吞吐效果更佳)，并尽量保持4KiB对齐
- 对于写入大文件，设置parallel参数提升写入并发度，提升整体写入吞吐
- 对于大文件顺序读，建议设置参数readahead，如32m
- wb开启回写模式，减少内核态与用户态上下文切换次数，提升写入速度，但注意通过wb模式写入的文件不能进行覆盖写
- 对于大量非热点小文件(<256KiB)顺序/随机读, 大文件随机读场景，建议设置参数direct_read
- 对于热点小文件(<256KiB)顺序/随机读场景，不设置参数direct_read
- 对于有高IOPS的业务需求，建议采用异步IO，同时使用DirectIO模式，并根据实际情况调大max_background和congestion_threshold
- 开启keep_pagecache可以利用系统VFS pagecache，对于热点文件特别是作为web静态资源存储加速明显；
- 开启参数skip_ne_dir_lookup，这样可以减少lookup的操作时延；

## 内存使用量高，导致OOM
- 如果内存本身紧张，建议entry_timeout,attr_timeout,dcache_timeout参数设置为0s
- 检查是否开启readahead，如果开启，检查是否过大，调小该值
- 如果不是顺序读大文件的场景，建议设置direct_read参数

## 控制台有对应的目录，但是us3fs挂载路径没有
证明这是一个虚拟目录，需要在us3fs挂载路径执行mkdir <这个目录>来显示该目录，或者开启check_virtual_dir该参数，通过以这个目录为前缀来检测是否有这个前缀的文件，来决定是否"应该"展示该目录，但不建议这么操作，会增加us3fs的时延。在v1.5.4版本以后check_virtual_dir参数功能默认开启，并取消该参数，如果需要关闭该功能可以设置disable_check_vdir参数。

## 系统日志出现‘too many open files’

该场景出现在有大量随机IO读的情况下，需要调大系统对进程打开文件描述符数量的限制(一般为1024)。通过`ulimit -a`检查`open files`项，设置成65535或以上。设置方式:

- - 单次bash环境有效: `ulimit -n 65535; <us3fs 挂载命令>`;
  - 系统生效: 参考[Too many open files](https://askubuntu.com/questions/1182021/too-many-open-files);
