# API签名算法

UFileREST API 基于 HMAC（哈希消息身份验证码）密钥使用自定义HTTP
方案进行身份验证。要对请求进行身份验证，您首先需要合并请求的选定元素以形成一个字符串。然后，您可以使用Ucloud私有访问密钥来计算该字符串的HMAC。通常我们将此过程称为“签署请求”并且我们将输出HMAC算法称为“签名”，因为它会模拟真实签名的安全属性。最后，您可以使用本部分中介绍的语法，作为请求的参数添加此签名。

系统收到经身份验证的请求时，将提取您申领的Ucloud私有访问密钥，并以相同的使用方式将它用于计算已收到的消息的签名。然后，它会将计算出的签名与请求者提供的签名进行对比。如果两个签名相匹配，则系统认为请求者必须拥有对Ucloud私有访问密钥的访问权限，因此充当向其颁发密钥的委托人的颁发机构。如果两个签名不匹配，那么请求将被丢弃，同时系统将返回错误消息。

基于以上的原理，我们对空间管理和文件管理两套接口提供两种原理一样但是细节不同的鉴权过程。

## 空间管理签名算法

账户的公私钥可以在UCloud网站的管理-\>API密钥-\>查看API密钥找到。

空间管理的签名使用查询字符串的签名方式，客户端通过把签名字段作为一个query字段传递给服务端请求授权验证。携带了签名(Signature)的创建空间请求具有如下的格式:

    GET /Type=public&BucketName=demobucket&PublicKey=uclouddemo@mail.com45207436768156091&Action=CreateBucket&Signature=13f7989d4a5a8ued83c0e57ah43b3607bc506c7c HTTP/1.1

其中Signature的伪代码计算方式如下：

    //生成请求的 query 字典
    querys = {"PublicKey" : publicKey} + {其他 query 字段}
    //对 query 字典按照字典排序方式(lexicographical order)排序
    querys = querys.sort()
    //生成 signstring
    signstring = ""
    for key, value in querys {
       signstring += key + value
    }
    //将私钥加入签名
    signstring += privateKey
    //按照SHA1(RFC 3174)计算签名
    signature = sha1(signstring)
    //以16进制显示签名
    signature = HEX(signature)

其他空间管理API的签名均可使用同样的计算方式生成。

## 文件管理签名算法

文件管理的签名有两种不同的方式通过HTTP请求传递给服务器，分别是身份验证标头和查询字符串请求身份验证替代项。

### 身份验证标头

该方式使用Authorization头部字段传递签名数据，并放置于各HTTP请求的报文头中，如下图所示，身份验证标头具有以下形式:

    Authorization: UCloud UCloudPublicKey:Signature

其中，Signature是一个哈希值，具体为请求中特定元素的HMAC-SHA1（RFC2104），因此Signature会因请求不同而异。如果客户端请求中随附的Signature与服务端计算出的Signature相匹配，则证明请求者拥有UCloud允许的访问权限。以下是Authorization身份验证标头构造的伪代码

    Authorization = "UCloud" + " " + UCloudPublicKey + ":" + Signature
    Signature = Base64( HMAC-SHA1( UCloudPrivateKey, UTF-8-Encoding-Of( StringToSign ) ) )
    StringToSign = HTTP-Verb + "\n" +
        Content-MD5 + "\n" +
        Content-Type + "\n" +
        Date + "\n" +
        CanonicalizedUCloudHeaders +
        CanonicalizedResource
    CanonicalizedUCloudHeaders = <described below>
    CanonicalizedResource = "/" + Bucket + "/" + Key

StringToSign中包括两类标头元素：

一类是位置标头，仅有3个，分别是Content-MD5、Content-Type和Date，在StringToSign中不包括这些标头的名称，仅包括它们在请求中的值，如果请求中这些标头不存在，需要用空字符串("")代替；

另一类是UCloud附加标头，以“X-UCloud-”开头，此类标头需要按照下面指定的方法构造CanonicalizedUCloudHeaders字符串后加入到StringToSign中。

**note**

1.  如果位置标头不在请求中（例如，Content-Type或Content-MD5对于PUT请求是可选的，并且对于GET请求没有任何意义），必须使用空字符串""替换该位置；
2.  BASE64使用standardbase64，不是URLSafe的base64算法，下同；
3.  当使用POST表单上传时，签名使用的Content-Type字段应该是form参数中的Content-Type字段(即文件本身的mimetype)，而非HTTP请求的Content-Type。

**计算CanonicalizedUCloudHeaders步骤**

1.将每个以X-UCloud-开头的HTTP标头名称转换为小写。例如，“X-UCloud-Date”改为“x-ucloud-date”。

2.根据标头名称按字典顺序排列标头集。

3.按照RFC2616中第4.2节中的规定，将相同名称的标头字段合并为一个“header-name:comma-separated-value-list”对，各值之间不留空格。

例如，可以将元数据标头“x-ucloud-meta-username:fred”和“x-ucloud-meta-username:barney”合并为单个标头“x-ucloud-meta-username:fred,barney”。

4.通过将折叠空格（包括换行符）替换为单个空格，“展开”跨多个行的长标头（按照RFC2616中第4.2节允许的方式）。

5.删除标头中冒号周围的空格。例如，标头“x-ucloud-meta-username:fred,barney”改为“x-ucloud-meta-username:fred,barney”。

6.最后，请向生成的列表中的每个标准化标头附加换行字符(U+000A)。通过将此列表中所有的标头规范化为单个字符串，构建CanonicalizedUcloudHeaders元素。

**示例**

1、确定使用API接口，例如使用PUTFile接口，签名前的请求如下

    PUT /demokey HTTP/1.1
    Host: demobucket.ufile.ucloud.cn
    Content-Length: 11434
    Content-Type: image/jpeg
    X-UCloud-Foo: foo
    X-UCloud-Bar: bar1
    X-UCloud-Bar: bar2

2、拼接签名字符串对照step1的请求中的各个参数，程序中的各变量取值如下（采用伪代码描述）

    bucket = "demobucket"
    key = "demokey"
    http_verb = "PUT"
    content_md5 = "" 
    content_type = "image/jpeg"
    date = "" 
    canonicalized_ucloud_headers = "x-ucloud-foo:foo" + "\n" + "x-ucloud-bar:bar1,bar2" + \n"
    canonicalized_resource = "/" + "demobucket" + "/" + "demokey"
    string2sign = "PUT" + "\n"
        + "" + "\n"
        + "image/jpg" + "\n"
        + "" + "\n"
        + "x-ucloud-foo:foo" + "\n" + "x-ucloud-bar:bar1,bar2" + "\n"
        + "/demobucket/demokey"

即

    string2sign = "PUT\n\nimage/jpeg\n\nx-ucloud-foo:foo\nx-ucloud-bar:bar1,bar2\n/demobucket/demokey"

3、hmac-sha1运算

使用ucloud分配给您的私钥对签名字符串做hmac-sha1运算

    hmacstring = hmac-sha1(privateKey, string2sign)

4、base64运算

对生成的hmacstring做base64运算

    base64string = base64(hmacstring) = S5FVD2w613MKb/hisjaqHdjvn9U=

5、生成最终签名格式

    signature = UCloud demouser@ucloud.cn13424346821929713944:S5FVD2w613MKb/hisjaqHdjvn9U=

6、生成带签名的HTTP请求

    PUT /demokey HTTP/1.1
    Host: demobucket.ufile.ucloud.cn
    Content-Length: 11434
    Content-Type: image/jpeg
    X-UCloud-Foo: foo
    X-UCloud-Bar: bar1
    X-UCloud-Bar: bar2
    Authorization: UCloud demouser@ucloud.cn13424346821929713944:S5FVD2w613MKb/hisjaqHdjvn9U=

### 查询字符串请求身份验证替代项

可以通过传递请求信息作为查询字符串参数，而不是使用AuthorizationHTTP标头来验证特定类型的请求。这在允许第三方浏览器直接访问您的UFile私有空间的文件，而无需代理请求时非常有用。其概念是构建一个“预签名”的请求并将其编码为最终用户浏览器可检索的URL。此外，您还可以通过指定过期时间来限制预签名请求。

以下是查询字符串经身份验证的UFile REST请求示例。

    GET /demokey.jpg?UCloudPublicKey=AKIAIOSFODNN7EXAMPLE&Expires=1141889120&Signature=vjbyPxybdZaNmGa%2ByT272YEAiv4%3D HTTP/1.1
    Host: demobucket.s3.ucloud.cn
    Date: Mon, 26 Mar 2007 19:37:58 +0000

查询字符串请求身份验证方法与普通的方法稍有差异，不同之处仅在于Signature请求参数和StringToSign元素的格式。下面的伪语法演示了查询字符串请求身份验证方法。

``` 
Signature = URL-Encode( Base64( HMAC-SHA1( UCloudPrivateKey, UTF-8-Encoding-Of( StringToSign ) ) ) );
StringToSign = HTTP-VERB + "\n" +
    "\n" +
    "\n" +
    Expires + "\n" +
    CanonicalizedUCloudHeaders +
    CanonicalizedResource; 
```

请注意，在StringToSign中，HTTPDate位置元素已替换为Expires。CanonicalizedUCloudHeaders和CanonicalizedResource是相同的。

## 文件ETag生成方法

文件ETag可用于查询文件是否可以进行秒传。

具体算法的伪代码如下:

    if filesize<=4MB:
        hash = base64_url_safe(blkcnt, sha(file))
    else:
        hash = base64_url_safe(blkcnt, sha(sha(blk0), sha(blk1)...))
    字段含义
        blkcnt:文件以4MB为一个块进行切分后的块个数。
        blkN:第N(N>=0)个数据块的数据。
