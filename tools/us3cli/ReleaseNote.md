1.2.0:

新增：

- 增加了动态进度条显示开关，默认开启
- du命令增加查看目录数据量功能
- ls命令新增查看projecdid功能
- ls展示文件列表新增文件数目显示信息
- cp命令增加自动识别mimetype功能
- sync命令新增指定storageclass（存储类型），mimetype（多媒体文件格式），metadata（元数据）上传功能
- sync命令新增mimetype自动识别功能
- 配置项新增内容加密、https以及代理功能

修改：

- 修改ls下展示文件激活信息，修改激活时间格式，新增Restoring标志，表示文件正在激活中
- 修改sync中的remote模式为local模式，功能不变

删除：

- 删除cp中的hit秒传功能