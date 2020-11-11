

# AWS S3 协议支持说明

### 支持的 API

US3 目前的 S3 协议模块对标准 S3 协议的支持如下表：

| **编号** | **API名字**               | **备注说明**                                                 |
| :------: | ------------------------- | ------------------------------------------------------------ |
|    1     | GET Bucket service        | 用于获取 Bucket 列表，只能获取公私钥或者 Token 拥有者创建的 Bucket |
|    2     | GET Bucket location       | 返回所在地域名，不建议依赖该 API                              |
|    3     | GET Bucket acl            | 没有太多意义，主要为了支持 S3 Browser 而实现，Permission字段永远为 “FULL_CONTROL” |
|    4     | GET Bucket versioning     | 没有太多意义，主要为了支持 S3 Browser 而实现，Status 字段永远为空字符串 |
|    5     | GET Object acl            | 没有太多意义，主要为了支持 S3 Browser 而实现，Permission 字段永远为 “FULL_CONTROL” |
|    6     | HEAD Object               | 参考[US3 兼容 S3 API - v2.2.pdf](http://ufile-release.cn-bj.ufileos.com/s3%2FUS3%20%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.2.pdf) |
|    7     | PUT Object                | 参考[US3 兼容 S3 API - v2.2.pdf](http://ufile-release.cn-bj.ufileos.com/s3%2FUS3%20%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.2.pdf) |
|    8     | POST Object               | 参考[US3 兼容 S3 API - v2.2.pdf](http://ufile-release.cn-bj.ufileos.com/s3%2FUS3%20%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.2.pdf) |
|    9     | PUT Object copy           | 参考[US3 兼容 S3 API - v2.2.pdf](http://ufile-release.cn-bj.ufileos.com/s3%2FUS3%20%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.2.pdf) |
|    10    | GET Object                | 参考[US3 兼容 S3 API - v2.2.pdf](http://ufile-release.cn-bj.ufileos.com/s3%2FUS3%20%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.2.pdf) |
|    11    | List Objects              | 参考[US3 兼容 S3 API - v2.2.pdf](http://ufile-release.cn-bj.ufileos.com/s3%2FUS3%20%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.2.pdf) |
|    12    | DELETE Object             | 参考[US3 兼容 S3 API - v2.2.pdf](http://ufile-release.cn-bj.ufileos.com/s3%2FUS3%20%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.2.pdf) |
|    13    | Delete Multiple Objects   | 参考[US3 兼容 S3 API - v2.2.pdf](http://ufile-release.cn-bj.ufileos.com/s3%2FUS3%20%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.2.pdf) |
|    14    | Initiate Multipart Upload | 参考[US3 兼容 S3 API - v2.2.pdf](http://ufile-release.cn-bj.ufileos.com/s3%2FUS3%20%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.2.pdf) |
|    15    | Upload Part               | 参考[US3 兼容 S3 API - v2.2.pdf](http://ufile-release.cn-bj.ufileos.com/s3%2FUS3%20%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.2.pdf) , **注意目前只支持 8MB 大小的分片!!!!** |
|    16    | Complete Multipart Upload | 参考[US3 兼容 S3 API - v2.2.pdf](http://ufile-release.cn-bj.ufileos.com/s3%2FUS3%20%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.2.pdf) |
|    17    | Abort Multipart Upload    | 参考[US3 兼容 S3 API - v2.2.pdf](http://ufile-release.cn-bj.ufileos.com/s3%2FUS3%20%E5%85%BC%E5%AE%B9S3%20API%20-%20v2.2.pdf) |

注意:

* PUT Object 目前仅支持 1GB 大小文件，如果需要上传大于 1GB 的文件，请采用分片上传的 API；

* POST Object 目前仅支持最大 32MB 文件的上传；

* 目前US3 S3 不支持多版本功能；

* ~~目前US3 S3 不支持用户自定义元数据功能，如Header头指定x-amz-meta-*~~；

* ~~目前US3 S3 不支持存储类型，默认为标准类型~~，存储类型转换规则参考【存储类型转换规则】；

* ~~目前US3 S3 不支持x-amz-content-sha256为`UNSIGNED-PAYLOAD`的签名~~；

* 目前不支持 S3 API 的 MD5 校验（原因跟 S3 的 ETag 计算方式有差异），建议关闭:

      例如AWS S3 Java SDK:
      System.setProperty(SkipMd5CheckStrategy.DISABLEGETOBJECTMD5VALIDATION_PROPERTY,"");
      
      System.setProperty(SkipMd5CheckStrategy.DISABLEPUTOBJECTMD5VALIDATION_PROPERTY,"");

### 仅仅支持签名 V4

支持 V4 签名的场景：

1. URL 中携带参数（URL Query 部分的 x-amz-credential 字段）；

2. POST（表单中 x-amz-credential 域）;

3. Header 中携带参数（Authorization字段);

### S3 的 AccessKeyID 和 SecretAccessKey 说明

S3 的 AccessKeyID（或称AccessKey）和 SecretAccessKey（或称SecretKey）对应就是 UCloud 的 API 公钥和私钥，或者是 US3服务提供的 Token 公钥和 Token 私钥；

**注意：要求无论是 API 公私钥还是 Token 公私钥，要求操作的 bucket，必须满足以下条件:**

* 创建该 bucket 的账户与 API 公私钥的拥有者必须一致；

* 创建该 bucket 的账户与创建 Token 的账户必须一致；

### API支持路径风格和虚拟主机风格

**路径风格格式为: `http://\${Endpoint}/\${bucket名字}/\${key名字}`，bucket 名字作为路径使用的一部分。**
例如，AWS S3 Java SDK 在 UCloud 北京地域走外网使用 US3 S3 服务则设置如下：  

    
    "AWSCredential credentials = new BasicAWSCredentials(ACCESS_KEY,
    SECRET_KEY);  
    ClientConfiguration clientConfig = new ClientConfiguration();  
    ...  
    S3ClientOptions clientOptions = S3ClientOptions.builder().build();  
    clientOptions.setPathStyleAccess(true); // 表明使用路径风格API  
    AmazonS3 conn = new AmazonS3Client(credential, clientConfig);  
    conn.setS3ClientOptions(clientOptions);  
    conn.setEndpoint("s3-cn-bj.ufileos.com");"

**虚拟主机风格: http://${bucket名字}.${Endpoint}/${key名字}，类似US3目前使用的URL形式。**

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
|  8   |    巴西-圣保罗    |  s3-bra-saopaulo.ufileos.com  |               待支持                |
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

参考 [图片处理服务](/ufile/service/pic)

### 存储类型转换规则

| **US3存储类型**                  | **S3存储类型** | **US3对应S3默认存储类型** |
| --------------- | ------------- | --------------- |
| STANDARD        | STANDARD<br/>STANDARD_IA             | STANDARD               |
| IA              | ONEZONE_IA<br/>INTELLIGENT_TIERING<br/>REDUCED_REDUNDANCY             | ONEZONE_IA               |
| ARCHIVE         | GLACIER<br/>DEEP_ARCHIVE             | GLACIER               |
