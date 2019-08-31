{{indexmenu_n>2}}

# 上传文件

### 简单上传

简单上传指的是使用UFile API中的PutObject方法上传单个文件（Object）。简单上传适用于一次HTTP请求交互即可完成上传的场景，比如小文件（小于1GB）的上传。

说明

- 简单上传的API接口的详细信息请参见[PutFile](https://docs.ucloud.cn/api/ufile-api/put_file)。

- 大文件（大于5GB）的上传请使用分片上传。

- 命名限制：

- 使用UTF-8编码。

- 长度必须在1–1023字节之间。

- 不能以正斜线（/）或者反斜线（\\）字符开头。

- 为了防止第三方未经授权往您的Bucket里上传数据，UFile提供了Bucket和Object级别的访问权限控制。

- 在文件上传到UFile上后，您可以通过上传回调来向指定的应用服务器发起回调请求，进行下一步操作。

- 如果上传的是图片，您还可以进行图片处理。

### 表单上传

表单上传是指使用UFile API中的[PostFile](https://docs.ucloud.cn/api/ufile-api/post_file)请求来完成Object的上传，上传的Object不能超过1GB。

    说明 表单上传的API接口详细信息请参见PostFile。

适用场景

表单上传非常适合嵌入在HTML网页中来上传Object，比较常见的场景是网站应用，以招聘网站为例：

|            | 不使用表单上传                    | 表单上传 |
| ---------- | --------------------------------- | -------------------------- |
| 流程对比   | 网站用户上传简历。                | 网站用户上传简历。|
| :::           | 网站服务器回应上传页面。          | 网站服务器回应上传页面。 |
| :::           | 简历被上传到网站服务器。          | 简历上传到UFile。|
|  :::          | 网站服务器再将简历上传到UFile。   | |

从流程上来说，使用表单上传，少了一步转发流程，更加方便。
从架构上来说，原来的上传都统一走网站服务器，上传量过大时，需要扩容网站服务器。采用表单上传后，直接从客户端上传数据到UFile，上传量过大时，压力都在UFile上，由UFile来保障服务质量。

### 分片上传

UCloud UFile提供的分片上传（Multipart Upload）功能，可以将要上传的文件分成多个数据块（UFile里又称之为Part）来分别上传，上传完成之后再调用UFile的接口将这些Part组合成一个Object来达到断点续传的效果。


#### 适用场景

当使用简单上传（PutFile）功能来上传较大的文件到UFile的时候，如果上传的过程中出现了网络错误，那么此次上传失败，重试必须从文件起始位置上传。针对这种情况，您可以使用分片上传来达到断点续传的效果。

相对于其他的上传方式，分片上传适用于以下场景：

* 恶劣的网络环境：如手机端，当出现上传失败的时候，可以对失败的Part进行独立的重试，而不需要重新上传其他的Part。

* 加速上传：要上传到UFile的本地文件很大的时候，可以并行上传多个Part以加快上传。

* 流式上传：可以在需要上传的文件大小还不确定的情况下开始上传。这种场景在视频监控等行业应用中比较常见。


#### 分片上传流程：

将要上传的文件按照一定的大小分片。

* 初始化一个分片上传任务[InitiateMultipartUpload](https://docs.ucloud.cn/api/ufile-api/initiate_multipart_upload)

* 逐个或并行上传分片[UploadPart](https://docs.ucloud.cn/api/ufile-api/upload_par)

* 完成上传[FinishMultipartUpload](https://docs.ucloud.cn/api/ufile-api/finish_multipart_upload)


#### 该过程需注意以下几点：

* 除了最后一块Part，其他Part的大小不能小于4MB，否则会导致调用FinishMultipartUpload接口时失败。

* 要上传的文件切分成Part之后，文件顺序是通过上传过程中指定的partNumber来确定的，实际执行中并没有顺序要求，因此可以实现并发上传。

* 具体的并发个数并不是越多速度越快，要结合用户自身的网络情况和设备负载综合考虑。

* 默认情况下，已经上传但还没有调用FinishMultipartUpload的Part是不会自动回收的，因此如果要终止上传并删除占用的空间请调用AbortMultipartUpload。

### 上传后续操作

* 在文件上传到UFile上后，您可以通过上传回调来向指定的应用服务器发起回调请求，进行下一步操作。

* 如果上传的是图片，您还可以进行「图片处理」。

### 上传回调

UCloud UFile在上传文件完成的时候可以提供回调（Callback）给应用服务器。您只需要在发送给UFile的请求中携带相应的 Callback 参数，即能实现回调。




