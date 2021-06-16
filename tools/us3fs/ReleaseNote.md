# 历史版本

## US3FS v1.5.3

### 新特性

* 新增direct_read参数支持绕过us3fs cache直接读us3
* 简单的性能分析数据周期性输出，默认1h输出一次，可以通过perf_dump参数调整
* 创建目录的mime-type设置为"application/x-directory"，便于在console上进行区分

### 改进

* entry_timeout，attr_timeout，dcache_timeout参数默认开启，且设置为5min
* readahead参数修改支持字符串解析，如:16m(1MiB)，8k(8KiB)，512(512B)
* 自动补全缺失的"key"格式目录，减少lookup目录时两次head操作("key"和"key/"格式的)为一次
* 默认开启参数nodirbl的功能，并且取消该参数，替换为语义化参数check_virtual_dir
* 新增文件名后缀的过滤字典，减少head操作频率，目前仅过滤以".jpe"和".jpeg"为后缀的文件

### BUG修复
* 分配inode时未初始化指定的uid,gid信息，以及未初始化mtime,ctime
* 目录成员过多情况下读取不完整问题
* allow_other参数未生效问题

## US3FS v1.5.2

### 新特性

* 无

### BUG修复

* 修复了后端连接重置后读取文件导致崩溃的问题

## US3FS v1.5.1

### 新特性

* 支持使用GetFileList获取目录下文件；通过`--gfl`参数开启

### BUG修复

* 修复了`mv`空目录报错的问题。
* 取消了预读最大64MB的限制

## US3FS v1.5.0

### 新特性

* 支持US3后端动态分片
* 支持挂载时指定bucket前缀路径

## US3FS v1.4.0

### 新特性

* 支持使用本地缓存异步上传
* 支持小文件读取读已之写（Read-Your-Write）

### 改进

* 通过--level指定日志级别

### BUG修复

* 修复了文件名中有特殊字符无法写入的问题。
* 修复了写入过程中可能出现的进程中断的问题。
* 修复了在对象存储中创建同名的目录和文件，ls会显示两个目录的问题
* 修复了初始化分片上传失败会阻塞的问题
