
# 下载文件

### 简单下载

简单下载是通过UFile API的GetObject接口，下载已经上传的文件（Object），Object下载是使用HTTP的GET请求来完成的。

1. 说明

- 简单下载的API接口详细信息请参见[*GetFile*](#_4.5.1简单下载)。

- Object的URL生成规则请参考UFile的访问。

- 如果需要使用自定义域名来访问Object，请参考自定义域名访问UFile。

2. 下载的安全及授权

> 为了防止第三方未经授权从您的Bucket里下载数据，UFile提供了Bucket和Object级别的访问权限控制。详情请参见[*权限控制*](#_权限控制_1)。

3. 更多参考

如果需要实现断点下载请参考断点续传下载。

### 断点续传下载

UFile提供了从Object指定的位置开始下载的功能，在下载大的Object的时候，可以分多次下载。如果下载中断，重启的时候也可以从上次完成的位置开始继续下载。

和简单上传类似，您也需要对该Object有读权限。通过设置参数Range来支持断点续传，对于比较大的Object建议使用该功能。Range的定义可参考HTTP RFC。如果在请求头中使用Range参数，则返回消息中会包含整个文件的长度和此次返回的范围。例如：Content-Range: bytes 0–9/44，表示整个文件长度为44，此次返回的范围为0–9。如果不在范围内，则传送整个文件，并且不在结果中提及Content-Range，返回码为206。

#### 下载的安全及授权

> 为了防止第三方未经授权从您的Bucket里下载数据，UFile提供了Bucket和Object级别的访问权限控制。详情请参见[*权限控制*](#_权限控制_1)。

## 管理文件

### 管理文件元信息

文件元信息（Object Meta）是对上传到UFile的文件的属性描述。文件元信息可以在各种方式上传时进行设置。

| 名称             | 描述 |
| ---------------- | -------------------- |
| Content-Length   | 该 Object 大小 |
| Content-Type     | 该 Object 文件类型 |

更多参考

您也可以在以下操作中添加文件元信息：

- *简单上传*

- *分片上传和断点续传*

### 查看文件列表

您可以通过 UFile API 中的 PrefixFileList 接口列出您在存储空间（Bucket）中上传的文件（Object）。

> 说明 查看文件列表的 API 详细信息请参考 [*PrefixFileList*](#_7.7.4 前缀列表查询 - PrefixFileList)。

您可以调用通过 PrefixFileList 接口，一次性得到某一 Bucket 下最多 1000 个 Object 的列表。通过下面的三个参数，您可以实现多种列举功能：

| 名称     | 作用 |
| -------- | ---------------------------------------------------------------------------------------------------------------------- |
| prefix   | 限定返回的Object key必须以Prefix作为前缀。注意使用Prefix查询时，返回的key中仍会包含Prefix。utf-8编码，默认为空字符串 |
| marker   | 设定结果从Marker之后按字母排序的第一个开始返回。标志字符串，utf-8编码，默认为空字符串 |
| limit    | 文件列表数目，默认为100 |

### 删除文件

删除文件即删除上传到存储空间（Bucket）中的文件（Object）。

UFile允许您执行如下删除动作：

-   单个删除：指定某个Object进行删除。

-   自动删除：如果需要删除的Object数目很多，而且删除的Object有一定的规律，比如定期删除某些天之前的Object，或者是要清空整个Bucket，推荐使用生命周期管理来自动删除Object。设置了生命周期规则之后，UFile会根据规则自动删除到期的Object，从而极大减少您发送删除请求的次数，提高删除效率。

### 管理回源设置

通过回源设置，对于获取数据的请求以多种方式进行回源读取，满足您对于数据热迁移、特定请求重定向等需求。

对UFile的每条GET请求的URL按设定的规则进行匹配，然后按照设定的规则进行回源。最多配置5条规则，顺序匹配，直到匹配到有效规则。目前回源类型为镜像方式。

### 镜像方式

如果配置了镜像回写，则对一个不存在的文件进行GET操作时，会向源地址请求这个文件，返回给用户，并同时写入到UFile。

1. 使用场景

镜像回源主要用于无缝迁移数据到UFile，即服务已经在自己建立的源站或者在其他云产品上运行，需要迁移到UFile上，但是又不能停止服务，此时可利用镜像回写功能实现。具体场景分析如下：

- 源站有一批冷数据，同时在不断的生成新的热数据。

> 可以先通过迁移工具将冷数据迁移到UFile上，迁移工具为UFile Import
> Tool，同时配置镜像回写，将源站的地址配置到UFile上。当将域名切换到UFile上（或者UCloud的CDN，回源到UFile）之后，就算有一部分新生成的数据没有迁移过来，依然可以在UFile上正常访问到，且访问一次后文件就会存入到UFile。域名切换后，源站已经没有新的数据产生了，此时再扫描一次，将还没有导过来的数据一次性导入到UFile，然后将镜像回写配置删除。
>
> 如果配置的源站是IP地址，那么将域名迁移到UFile后还可以继续镜像到源站，但是如果配置的是一个域名，由于域名本身会解析到UFile或者CDN，那么镜像就失去作用了，在这种情况下，可以另外申请一个域名作为镜像的源站，这个域名与正在服务的域名解析到同一个IP地址，这样服务域名迁移的时候就可以继续镜像到源站了。

- 只切换源站的部分流量到UFile或者CDN，源站本身还在不断的产生数据。

> 迁移方式与上述方式类似，只是流量切换到UFile后，不要将镜像回写配置删掉，这样可以保证切换到UFile或者CDN的流量还是能够取到源站的数据。

##### 细节分析

- 只有当GetFile()本应该返回404的情况下，UFile才会执行镜像回源，向源站请求文件。

- 向源站请求的URL为MirrorURL+object，回写到UFile的文件名为“object”，例如bucket为example-bucket，配置了镜像回写，MirrorURL为 http://www.example-domain.com/ ，文件image/example\_object.jpg不在这个bucket里面，此时去下载这个文件时，UFile将向 http://www.example-domain.com/image/example\_object.jpg 发起GET请求，并将结果同时返回给用户以及写入到UFile，当下载完成后，这个文件就已经存在UFile上了，文件名为image/example\_object.jpg，此时相当于将源站的文件同名的迁移到了UFile上。如果MirrorURL带有path信息，比如 http://www.example-domain.com/dir1/ ，则与上例相同，UFile回源的URL为 http://www.example-domain.com/dir1/image/example\_object.jpg ，写入到UFile的object依然是image/example\_object.jpg，此时相当于将源站的某一个目录下的文件迁移到UFile上。

- 传给UFile的header信息不会传递给源站，querystring信息是否会传递给源站取决于控制台回源规则中的配置。

- 如果源站是chunked编码返回，那么UFile返回给用户的也是chunked编码。

- UFile会将源站的以下头信息返回并存为UFile的头信息：

```
Content-Type

Content-Encoding

Content-DispositionCache-Control

ExpiresContent-LanguageAccess-Control-Allow-Origin
```

- 假设文件已经通过镜像回写到了UFile，如果源站的相同文件发生了变化，那UFile不会更新已经存在于UFile上的文件，因为此时文件已经在UFile上，不符合镜像回写的条件。

- 如果镜像源也不存在此文件，即镜像源返回给UFile的http status为404，那么UFile也返回404给用户，如果是其他非200的状态码（包括因为网络原因等获取不到文件的错误情况），UFile将返回424给用户，错误码为“MirrorFailed”。
