# 上传策略说明

{{indexmenu_n>9}}

UFile上传策略(PutPolicy)用于上传API执行一些用户可以指定的操作,这些操作将会在上传动作完成后(部分动作是在开始上传前执行)触发,完成用户指定的操作。

可以使用PutPolicy的API包括: PutFile、FinishMultipartUpload

## UFile上传回调

上传策略最重要的功能，是可以回调其他服务(callback)，对上传的文件进行处理。

该策略指定在上传文件完成后(携带可能的参数)去请求一个用户指定的服务地址(目前仅支持http,并且仅支持请求一个指定服务)。在得到用户服务器的回应(必须是application/json格式)后将用户服务器的返回值透传给用户。回调服务的地址使用json格式进行封装，格式如下：

    {
     "callbackUrl" : "http://test.ucloud.cn",   //指定回调服务的地址
     "callbackBody" : "key1=value1&key2=value2" //传递给回调服务的参数
    }

携带上传策略的上传请求和不带上传策略的上传请求之间的区别是在授权字段Authorization部分,API其他格式均保持不变。

不需要上传策略时，我们在上传时的Authorization格式为:

Authorization: UCloud publickey:signature

如需使用上传策略,则格式变为:

Authorization: UCloud publickey:signature:encodedPutPolicy

其中，encodedPutPolicy = base64(json\_ encode(put\_ policy))
(注意json格式请使用压缩后的格式,不要携带空白字符,除非key/value本身是含有空白字符的字符串。这里的base64是URLSafe的base64)

此外,旧有方式的签名字符串计算方式是：

    signstring = HTTP-Verb + "\n" +
          Content-MD5 + "\n" +
          Content-Type + "\n" +
          Date + "\n" +
          CanonicalizedUCloudHeaders +
          CanonicalizedResource

当上传请求需要执行上传策略的时候,签名字符串其他部分不变,需要在末尾追加上传策略的base64字符串,即

signstring\_ with\_ putpolicy = signstring + base64(json\_ encode(put\_
policy))

## 用法示例

假设上传的文件为：flower.jpg

上传策略为：

"callbackUrl" : "
<http:%%//%%inner.umedia.ucloud.com.cn/CreateUmediaTask>",

"callbackBody" :
"url=<http:%%//%%demo.ufile.ucloud.cn/test.mp4&patten_name=mypolicy>"

则没有上传策略的上传请求是:

    PUT /flower.jpg HTTP/1.1
    Content-Length: 123456
    Content-Type: image/jpeg
    Host: test.ufile.ucloud.cn
    Authorization: UCloud aGVsbHdvZGhhZGhhc2RoYWRzZGFkaHNkaGFkaGhkaGxrc2Rh:bTgzdWhkZGlsYS9kLmFkYWRhc2Ruaw==

而携带了上传策略的上传请求是:

``` 
PUT /flower.jpg HTTP/1.1
Content-Length: 123456
Content-Type: image/jpeg
Host: test.ufile.ucloud.cn
Authorization: UCloud aGVsbHdvZGhhZGhhc2RoYWRzZGFkaHNkaGFkaGhkaGxrc2Rh:ZGFkLHBwMz0xZGthZGFkYXNkYQ==:XCJjYWxsYmFja1VybFwiOlwiIGh0dHA6Ly9pbm5lci51bWVkaWEudWNsb3VkLmNvbS5jbi9DcmVhdGVVbWVkaWFUYXNrXCIsXCJjYWxsYmFja0JvZHlcIjpcInVybD1odHRwOi8vZGVtby51ZmlsZS51Y2xvdWQuY24vdGVzdC5tcDQmIHBhdHRlbl9uYW1lPW15cG9saWN5XCI=

```

注：签名与bucket相关，示例中签名仅作参考。
