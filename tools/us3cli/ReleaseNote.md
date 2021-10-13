# 1.3.0
## 修改

- cat命令目前可以并发下载
- 修复了du命令的一个bug，该bug会导致子账号拿不到桶的数据统计信息
- 现在可以并发的上传和下载，但是您无法同时执行两个同样的cp, sync,或du命令。（例如：您无法同时执行两个 cp ./test us3://bucket/prefix/test 命令）
- 现在modify命令在指定--recursive参数时可以批量的修改同一个prefix下的所有对象。
- 优化us3cli的升级逻辑，修复升级失败后再次打开cli出现的找不到可执行文件的错误
- 修复在根目录执行拷贝操作时通配符失效的问题
- 修复代理配置无法清除的问题

## 新增

- 新增了四个对于token的操作: create-token, delete-token, update-token, describe-token, 分别对应于token的增删改查。
- 新增了三个新的区域: jpn-tky, th-bkk, inspurcloud。
- 新增了ls命令的一个参数: --prefix-file-list, 设置这个参数将会调用prefix-file-list接口来拉取列表。

# 1.2.2
## 修改

- 修复上传重试失败问题
- 修复进度条展示出现负数问题
- 修复存在空目录时下载报错问题

## 新增

- 新增.svg文件的mimetype自动识别

# 1.2.0

## 新增

- 增加了动态进度条显示开关，默认开启
- du命令增加查看目录数据量功能
- ls命令新增查看projecdid功能
- ls展示文件列表新增文件数目显示信息
- cp命令增加自动识别mimetype功能
- sync命令新增指定storageclass（存储类型），mimetype（多媒体文件格式），metadata（元数据）上传功能
- sync命令新增mimetype自动识别功能
- 配置项新增内容加密、https以及代理功能

## 修改

- 修改ls下展示文件激活信息，修改激活时间格式，新增Restoring标志，表示文件正在激活中
- 修改sync中的remote模式为local模式，功能不变

## 删除

- 删除cp中的hit秒传功能