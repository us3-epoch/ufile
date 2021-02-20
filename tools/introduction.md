

# 常用工具

除使用 US3 管理控制台之外，您还可以通过 US3 提供的各类常用工具对 US3 存储空间及空间内的文件对象进行管理，或帮助您接入各类场景，实现数据在云端的快速读写。

**US3 产品底层升级后，推出 us3cli、us3fs 工具，便于用户对存储空间、文件进行管理操作，操作方式更灵活。推出 us3hadoop 大数据适配工具，便于各类大数据计算框架访问及读写 US3 上的数据。**

原有空间管理工具 bucketmgr 与文件管理工具 filemgr 仍可正常使用。

## 管理工具

|工具    |说明 |
|------------------------------------------|--------------------------------------------  |
|[US3CLI 命令行工具](/ufile/tools/us3cli/introduction)  |统一管理工具 US3CLI 可帮助用户进行 US3 存储空间和文件的统一管理，支持丰富的存储空间以及文件对象操作。 |
|[US3FS 文件挂载工具](/ufile/tools/us3fs/introduction)  |文件系统挂载工具 US3FS 可帮助用户在 Linux 环境下以 POSIX 协议方式挂载文件目录，对文件对象进行操作。 |
|[US3SYNC 迁移工具](/ufile/tools/us3sync/introduction)   |迁移工具 US3SYNC 可帮助用户将数据迁移至 US3 的存储空间中。 |
|[US3Hadoop 大数据适配工具](/ufile/tools/us3hadoop/introduction)   |大数据适配工具 US3Hadoop 可帮助用户在 Hadoop 中以文件系统的方式访问 US3。 |

## 其他工具

以下工具目前已无版本更新计划，请根据需求合理选择使用：

|工具    |说明 |
|------------------------------------------|--------------------------------------------  |
|[BUCKETMGR 空间管理](ufile/tools/tools/tools_bcket)  |空间管理工具可帮助用户获取存储相关信息并进行空间管理。 |
|[FILEMGR 文件管理](ufile/tools/tools/tools_file)  |文件管理工具用于在命令行做上传/下载/删除等针对文件的操作。 |
|[一致性比对工具](/ufile/tools/tools/consistency)  |一致性比对工具用于比对用户本地与 US3 存储空间中的文件是否一致。 |
|[原迁移工具](ufile/tools/tools/ufile_import)   |迁移工具可帮助用户将数据迁移至 US3 的存储空间中。 |
