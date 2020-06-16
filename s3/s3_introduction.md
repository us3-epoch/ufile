

# 支持AWS S3 协议说明

### 支持的API

UFile目前的S3协议模块对标准S3协议的支持如下表：

| **编号** | **API名字**               | **备注说明**                                                 |
| :------: | ------------------------- | ------------------------------------------------------------ |
|    1     | GET Bucket service        | 用于获取Bucket列表，只能获取公私钥或者Token拥有者创建的Bucket |
|    2     | GET Bucket location       | 返回所在地域名，不建议依赖该API                              |
|    3     | GET Bucket acl            | 没有太多意义，主要为了支持S3 Browser而实现，Permission字段永远为“FULL_CONTROL” |
|    4     | GET Bucket versioning     | 没有太多意义，主要为了支持S3 Browser而实现，Status字段永远为空字符串 |
|    5     | GET Object acl            | 没有太多意义，主要为了支持S3 Browser而实现，Permission字段永远为“FULL_CONTROL” |
|    6     | HEAD Object               | 参考[UFile S3 兼容API文档-2.1.pdf](http://ufile-release.cn-bj.ufileos.com/s3/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.1.pdf) |
|    7     | PUT Object                | 参考[UFile S3 兼容API文档-2.1.pdf](http://ufile-release.cn-bj.ufileos.com/s3/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.1.pdf) |
|    8     | POST Object               | 参考[UFile S3 兼容API文档-2.1.pdf](http://ufile-release.cn-bj.ufileos.com/s3/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.1.pdf) |
|    9     | PUT Object copy           | 参考[UFile S3 兼容API文档-2.1.pdf](http://ufile-release.cn-bj.ufileos.com/s3/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.1.pdf) |
|    10    | GET Object                | 参考[UFile S3 兼容API文档-2.1.pdf](http://ufile-release.cn-bj.ufileos.com/s3/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.1.pdf) |
|    11    | List Objects              | 参考[UFile S3 兼容API文档-2.1.pdf](http://ufile-release.cn-bj.ufileos.com/s3/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.1.pdf) |
|    12    | DELETE Object             | 参考[UFile S3 兼容API文档-2.1.pdf](http://ufile-release.cn-bj.ufileos.com/s3/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.1.pdf) |
|    13    | Delete Multiple Objects   | 参考[UFile S3 兼容API文档-2.1.pdf](http://ufile-release.cn-bj.ufileos.com/s3/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.1.pdf) |
|    14    | Initiate Multipart Upload | 参考[UFile S3 兼容API文档-2.1.pdf](http://ufile-release.cn-bj.ufileos.com/s3/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.1.pdf) |
|    15    | Upload Part               | 参考[UFile S3 兼容API文档-2.1.pdf](http://ufile-release.cn-bj.ufileos.com/s3/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.1.pdf) , **注意目前只支持8MB大小的分片!!!!** |
|    16    | Complete Multipart Upload | 参考[UFile S3 兼容API文档-2.1.pdf](http://ufile-release.cn-bj.ufileos.com/s3/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.1.pdf) |
|    17    | Abort Multipart Upload    | 参考[UFile S3 兼容API文档-2.1.pdf](http://ufile-release.cn-bj.ufileos.com/s3/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.1.pdf) |

注意:

* PUT Object 目前仅支持1GB大小文件，如果需要上传大于1GB的文件，请采用分片上传的API

* POST Object目前仅支持最大32MB文件的上传；

* 目前UFile S3 不支持多版本功能；

* ~~目前UFile S3 不支持用户自定义元数据功能，如Header头指定x-amz-meta-*~~；

* ~~目前UFile S3 不支持存储类型，默认为标准类型~~，存储类型转换规则参考【存储类型转换规则】；

* ~~目前UFile S3 不支持x-amz-content-sha256为`UNSIGNED-PAYLOAD`的签名~~；

* 目前不支持S3 API的MD5校验(原因跟S3的ETag计算方式有差异)，建议关闭:

      例如AWS S3 Java SDK:
      System.setProperty(SkipMd5CheckStrategy.DISABLEGETOBJECTMD5VALIDATION_PROPERTY,"");
      
      System.setProperty(SkipMd5CheckStrategy.DISABLEPUTOBJECTMD5VALIDATION_PROPERTY,"");

### 仅仅支持签名V4

支持V4签名的场景：

1. URL 中携带参数（URL Query部分的x-amz-credential字段）；

2. POST（表单中x-amz-credential域）;

3. Header 中携带参数（Authorization字段);

### S3的AccessKeyID和SecretAccessKey说明

S3的AccessKeyID(或称AccessKey)和SecretAccessKey(或称SecretKey)对应就是UCloud的API公钥和私钥，或者是UFille服务提供的Token公钥和Token私钥；

**注意：要求无论是API公私钥还是Token公私钥，要求操作的bucket，必须满足以下条件:**

* 创建该bucke的账户与API公私钥的拥有者必须一致；

* 创建该bucke的账户与创建Token的账户必须一致；

### API支持路径风格和虚拟主机风格

**路径风格格式为: http://\${Endpoint}/\${bucket名字}/\${key名字}，bucket名字作为路径使用的一部分;**

    例如: AWS S3 Java SDK在UCloud北京地域走外网使用UFile S3服务则设置如下：  
    "AWSCredential credentials = new BasicAWSCredentials(ACCESS_KEY,
    SECRET_KEY);  
    ClientConfiguration clientConfig = new ClientConfiguration();  
    ...  
    S3ClientOptions clientOptions = S3ClientOptions.builder().build();  
    clientOptions.setPathStyleAccess(true); // 表明使用路径风格API  
    AmazonS3 conn = new AmazonS3Client(credential, clientConfig);  
    conn.setS3ClientOptions(clientOptions);  
    conn.setEndpoint("s3-cn-bj.ufileos.com");"

**虚拟主机风格: http://${bucket名字}.${Endpoint}/${key名字}，类似UFile目前使用的URL形式；**

### 接入域名（Endpoint）

| **编号** |       **地域**        |        **外网Endpoint**        |            **内网Endpoint**             |
| ---- | ----------------- | -------------------------- | ----------------------------------- |
|  1   |    中国-北京二    |    s3-cn-bj.ufileos.com    |    internal.s3-cn-bj.ufileos.com    |
|  2   |     中国-上海     |   s3-cn-sh2.ufileos.com    |   internal.s3-cn-sh2.ufileos.com    |
|  3   |     中国-广州     |           待支持           |    internal.s3-cn-gd.ufileos.com    |
|  4   |     中国-香港     |     s3-hk.ufileos.com      |     internal.s3-hk.ufileos.com      |
|  5   |    越南-胡志明    |   s3-vn-sng.ufileos.com    |   internal.s3-vn-sng.ufileos.com    |
|  6   |  尼日利亚-拉各斯  | s3-afr-nigeria.ufileos.com | internal.s3-afr-nigeria.ufileos.com |
|  7   |     韩国-首尔     |  s3-kr-seoul.ufileos.com   |  internal.s3-kr-seoul.ufileos.com   |
|  8   |    巴西-圣保罗    |  bra-saopaulo.ufileos.com  |               待支持                |
|  9   |    美国-洛杉矶    |    s3-us-ca.ufileos.com    |    internal.s3-us-ca.ufileos.com    |
|  10  |    美国-华盛顿    |    s3-us-ws.ufileos.com    |    internal.s3-us-ws.ufileos.com    |
|  11  |    阿联酋-迪拜    |  s3-uae-dubai.ufileos.com  |  internal.s3-uae-dubai.ufileos.com  |
|  12  |   德国-法兰克福   |   s3-ge-fra.ufileos.com    |   internal.s3-ge-fra.ufileos.com    |
|  13  | 印度尼西亚-雅加达 | s3-idn-jakarta.ufileos.com | internal.s3-idn-jakarta.ufileos.com |
|  14  |     印度-孟买     | s3-ind-mumbai.ufileos.com  | internal.s3-ind-mumbai.ufileos.com  |
|  15  |      新加坡       |     s3-sg.ufileos.com      |     internal.s3-sg.ufileos.com      |

注意: *目前中国-北京二，中国-香港，越南-胡志明，韩国-首尔，巴西-圣保罗，美国-洛杉矶，美国-华盛顿地域已经支持https协议，其他地域可支持路径风格https，后续支持虚拟主机风格https (所有地域内网不支持https)*

### 回调扩展功能支持

| **请求形式 API 名字**                  | **PUT Object** | **POST Object** | **Complete Multipart Upload** |
| ------------------------------- | ------------- | --------------- | ----------------------------- |
| **在 URL 中携带参数**                 | √             | ×               | √                             |
| **在 Header 中携带参数**              | √             | ×               | √                             |
| **在 POST 请求的 body 中使用表单域来携带参数** | ×             | √               | ×                             |

    √:支持
    ×:不支持

### 图片操作支持

参考[UFile官方文档图片处理服务](https://docs.ucloud.cn/ufile/service/pic)

### 存储类型转换规则

| **UFile存储类型**                  | **S3存储类型** | **UFile对应S3默认存储类型** |
| --------------- | ------------- | --------------- |
| STANDARD        | STANDARD<br/>STANDARD_IA             | STANDARD               |
| IA              | ONEZONE_IA<br/>INTELLIGENT_TIERING<br/>REDUCED_REDUNDANCY             | ONEZONE_IA               |
| ARCHIVE         | GLACIER<br/>DEEP_ARCHIVE             | GLACIER               |
