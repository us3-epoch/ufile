# 常见问题

## 挂载失败，日志出现“internal error”
所在地域不支持列表服务，可指定`--gfl`重新挂载。（*不支持ListObjects API地域*）

## 挂载后发现跟控制台文件信息不一致
该问题是由于默认开启entry_timeout,attr_timeout,dcache_timeout参数，并5min过期时间，如果对一致性敏感，建议这些参数设置为0s来关闭

## 降低读写时延的方法
- 开启entry_timeout,attr_timeout,dcache_timeout参数，减少lookup穿透到us3增加的索引时延
- 应用层系统调用写入尽量采用大IO(>=128KiB，如果内存允许使用>=1MiB的提升吞吐效果更佳)，并尽量保持4KiB对齐
- 对于写入大文件，设置parallel参数提升写入并发度，提升整体写入吞吐
- 对于大文件顺序读，建议设置参数readahead，如32m
- 对于大量非热点小文件(<256KiB)顺序/随机读, 大文件随机读场景，建议设置参数direct_read
- 对于热点小文件(<256KiB)顺序/随机读场景，不设置参数direct_read
- async_read参数开启异步读模式，有利于读请求
- wb开启回写模式，减少内核态与用户态上下文切换次数，提升写入速度

## 内存使用量高，导致OOM
- 如果内存本身紧张，建议entry_timeout,attr_timeout,dcache_timeout参数设置为0s
- 检查是否开启readahead，如果开启，检查是否过大，调小该值
- 如果不是顺序读大文件的场景，建议设置direct_read参数

## 控制台有对应的目录，但是us3fs挂载路径没有
证明这是一个虚拟目录，需要在us3fs挂载路径执行mkdir <这个目录>来显示该目录，或者开启check_virtual_dir该参数，通过以这个目录为前缀
来检测是否有这个前缀的文件，来决定是否"应该"展示该目录，但不建议这么操作，会增加us3fs的时延

