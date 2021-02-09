# 常见问题

## 对US3进行HDFS命令操作时报`ls: No FileSystem for scheme: us3`

请检查配置项`fs.us3.impl`是否配置。

## 对US3进行操作时报`java.lang.RuntimeException: java.lang.ClassNotFoundException: Class cn.ucloud.us3.fs.US3FileSystem not found`

检查适配器jar包是否放置在Hadoop查找的CLASS_PATH路径中。