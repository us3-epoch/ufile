### v1.3.0

## 新增

- 适配器增加从zookeeper获取vmds集群主节点信息的功能

### v1.2.0

## 重构

- 使用线上2.6.6版本的us3 sdk

### v1.1.0

## 功能

- 支持distcp校验拷贝功能

## 修复

- 日志格式打印错误

### v1.0.2

## 功能

- 支持开启md5计算，并保留其经过base64编码后结果到文件的元数据中

## 修复

- listStatus的Path是文件时，会返回空列表，正确的语义应该是只包含这个文件的列表
- 写入流的字节统计不准确
- 重命名时有相同文件名失败
- 列表服务响应变化导致的确保一致性误判而超时和目录偏移越界的问题
- 在缺失以`/`结尾的目录文件时，getFilsStatus触发的`dead loop`问题
- rename的目的端存在时抛出FileAlreadyExistsException，语义应该为false

## 优化

- 通知us3vmds逻辑从写入流解耦

### v1.0.1

## 功能

- getFileStatus
- listStatus
- mkdirs
- rename
- delete
- create
- open
- setOwner
- setPermission
- setReplication
- close
- write of OutputStream
- close of OutputStream
- read of InputStream
- seek of InputStream
- getPos of InputStream
- close of InputStream

