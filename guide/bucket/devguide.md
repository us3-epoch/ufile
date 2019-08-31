{{indexmenu_n>1}}

# 存储空间

### 创建存储空间

在上传文件（Object）到 UFile 之前，您需要使用 UFile API 中的 CreateBucket 接口来创建一个用于存储文件的存储空间（Bucket），存储空间具有各种配置属性，包括地域、访问权限以及其他元数据。或者使用[UFile控制台](https://console.ucloud.cn/ufile/ufile)来创建一个存储空间（Bucket），并设置存储空间的访问权限。

创建存储空间的 API 接口的详细信息请参见 [CreateBucket](https://help.aliyun.com/document_detail/31959.html#reference-wdh-fj5-tdb)。

使用限制

- 同一用户创建的存储空间总数不能超过 20 个。

- 每个存储空间的名字全局唯一，否则会创建失败。

- 存储空间的名称需要符合命名规范。

- 存储空间一旦创建成功，名称和所处地域不能修改。


