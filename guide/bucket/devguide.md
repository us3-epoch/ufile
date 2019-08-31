{{indexmenu_n>1}}

# 创建存储空间

在上传文件（Object）到 UFile 之前，您需要使用 UFile API 中的 CreateBucket 接口来创建一个用于存储文件的存储空间（Bucket），存储空间具有各种配置属性，包括地域、访问权限以及其他元数据。或者使用[UFile控制台](https://console.ucloud.cn/ufile/ufile)来创建一个存储空间（Bucket），并设置存储空间的访问权限。

创建存储空间的 API 接口的详细信息请参见 [CreateBucket](https://help.aliyun.com/document_detail/31959.html#reference-wdh-fj5-tdb)。

### 操作方式
|操作方式    |说明 |
|--------- |--------------------------------------------------------------------------------------------------------------- |
|控制台  |Web 应用程序，直观易用 |
|管理工具  |[管理工具](https://cms.docs.ucloudadmin.com/storage_cdn/ufile/tools/introduction) |
|API   |[API](https://docs.ucloud.cn/api/ufile-api/index) |
|SDK   |[SDK](https://cms.docs.ucloudadmin.com/storage_cdn/ufile/tools/sdk) |

#### 使用限制

* 同一用户创建的存储空间总数不能超过 20 个。

* 每个存储空间的名字全局唯一，否则会创建失败。

* 存储空间的名称需要符合命名规范。

* 存储空间一旦创建成功，名称和所处地域不能修改。


### 设置空间读写权限

您可以在创建存储空间（Bucket）时设置存储空间的访问权限（ACL），也可以在创建Bucket后根据自己的业务需求修改存储空间的ACL，该操作只有存储空间的拥有者可以执行。

存储空间有两种访问权限：

|空间类型    |权限对访问者的限制 |
|--------- |--------------------------------------------------------------------------------------------------------------- |
|私有空间  |只有该存储空间的拥有者可以对该存储空间内的文件进行写操作，所有文件须获得拥有者的API密钥授权才能访问。 |
|公有空间   |只有该存储空间的拥有者可以对该存储空间内的文件进行写操作，任何人（包括匿名访问者）都可以对该存储空间中的文件进行读操作。 |

### 获取空间地域信息

您可以通过UFile API的DescribeBucket接口获取存储空间（Bucket）所属的地域，即数据中心的物理位置信息。
获取存储空间地域信息的API详情请参考[DescribeBucket](https://docs.ucloud.cn/api/ufile-api/describe_bucket)。



