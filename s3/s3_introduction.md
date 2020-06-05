

# 支持AWS S3 协议说明

### 支持的API

UFile目前的S3协议模块对标准S3协议的支持如下表：

| **API名字**                 | **备注说明**                                 |
| ------------------------- | ---------------------------------------- |
| GET Bucket service        | 用于获取Bucket列表，只能获取公私钥或者Token拥有者创建的Bucket  |
| GET Bucket location       | 返回所在地域名，不建议依赖该API                        |
| GET Bucket acl            | 没有太多意义，主要为了支持S3 Browser而实现，Permission字段永远为“FULL_CONTROL” |
| GET Bucket versioning     | 没有太多意义，主要为了支持S3 Browser而实现，Status字段永远为空字符串 |
| GET Object acl            | 没有太多意义，主要为了支持S3 Browser而实现，Permission字段永远为“FULL_CONTROL” |
| HEAD Object               | 获取文件元数据信息                                |
| PUT Object                | 参考[UFile S3 兼容API文档-2.0.pdf](http://ufile-release.cn-bj.ufileos.com/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%202.0.pdf) |
| POST Object               | 参考[UFile S3 兼容API文档-2.0.pdf](http://ufile-release.cn-bj.ufileos.com/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%202.0.pdf) |
| PUT Object copy           | 参考[UFile S3 兼容API文档-2.0.pdf](http://ufile-release.cn-bj.ufileos.com/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%202.0.pdf) |
| GET Object                | 参考[UFile S3 兼容API文档-2.0.pdf](http://ufile-release.cn-bj.ufileos.com/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%202.0.pdf) |
| List Objects              | 参考[UFile S3 兼容API文档-2.0.pdf](http://ufile-release.cn-bj.ufileos.com/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%202.0.pdf) |
| DELETE Object             | 参考[UFile S3 兼容API文档-2.0.pdf](http://ufile-release.cn-bj.ufileos.com/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%202.0.pdf) |
| Delete Multiple Objects   | 参考[UFile S3 兼容API文档-2.0.pdf](http://ufile-release.cn-bj.ufileos.com/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%202.0.pdf) |
| Initiate Multipart Upload | 参考[UFile S3 兼容API文档-2.0.pdf](http://ufile-release.cn-bj.ufileos.com/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%202.0.pdf) |
| Upload Part               | 参考[UFile S3 兼容API文档-2.0.pdf](http://ufile-release.cn-bj.ufileos.com/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%202.0.pdf) , **注意目前只支持8MB大小的分片!!!!** |
| Complete Multipart Upload | 参考[UFile S3 兼容API文档-2.0.pdf](http://ufile-release.cn-bj.ufileos.com/UFile%E5%85%BC%E5%AE%B9S3%20API%20-%202.0.pdf) |

注意:

* PUT Object 目前仅支持16MB大小文件，如果需要上传大于16MB的文件，请采用分片上传的API

* POST Object目前仅支持最大32MB文件的上传；

* 目前UFile S3 不支持多版本功能；

* 目前UFile S3 不支持用户自定义元数据功能，如Header头指定x-amz-meta-*；

* 目前UFile S3 不支持存储类型，默认为标准类型；

* 目前UFile S3 不支持x-amz-content-sha256为`UNSIGNED-PAYLOAD`的签名；

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

|    地域    |         外网Endpoint         |             内网Endpoint              |
| :------: | :------------------------: | :---------------------------------: |
|  中国-北京二  |    s3-cn-bj.ufileos.com    |    internal.s3-cn-bj.ufileos.com    |
|  中国-上海   |   s3-cn-sh2.ufileos.com    |   internal.s3-cn-sh2.ufileos.com    |
|  中国-广州   |            待支持             |    internal.s3-cn-gd.ufileos.com    |
|  中国-香港   |     s3-hk.ufileos.com      |     internal.s3-hk.ufileos.com      |
|  越南-胡志明  |   s3-vn-sng.ufileos.com    |   internal.s3-vn-sng.ufileos.com    |
| 尼日利亚-拉各斯 | s3-afr-nigeria.ufileos.com | internal.s3-afr-nigeria.ufileos.com |
|  韩国-首尔   |  s3-kr-seoul.ufileos.com   |  internal.s3-kr-seoul.ufileos.com   |
|  巴西-圣保罗  |  bra-saopaulo.ufileos.com  |                 待支持                 |
|  美国-洛杉矶  |    s3-us-ca.ufileos.com    |    internal.s3-us-ca.ufileos.com    |

注意: *目前中国-北京二，中国-香港，越南-胡志明，韩国-首尔，巴西-圣保罗地域已经支持https协议，其他地域后续支持 (所有地域内网不支持https)*

### 回调扩展功能支持

| **请求形式 API名字**                  | **PUT Objec** | **POST Object** | **Complete Multipart Upload** |
| ------------------------------- | ------------- | --------------- | ----------------------------- |
| **在 URL 中携带参数**                 | √             | ×               | √                             |
| **在 Header 中携带参数**              | √             | ×               | √                             |
| **在 POST 请求的 body 中使用表单域来携带参数** | ×             | √               | ×                             |

    √:支持
    ×:不支持

### 图片操作支持

参考[UFile官方文档图片处理服务](https://docs.ucloud.cn/ufile/service/pic)


