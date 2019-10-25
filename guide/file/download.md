


# 下载文件

### 简单下载

简单下载是通过UFile API的GetObject接口，下载已经上传的文件（Object），Object下载是使用HTTP的GET请求来完成的。

* 简单下载的API接口详细信息请参见[GetFile](https://docs.ucloud.cn/api/ufile-api/get_file)。

* Object的URL生成规则请参考UFile的访问。

* 如果需要使用自定义域名来访问Object，请参考自定义域名访问UFile。

### 断点续传下载

UFile提供了从Object指定的位置开始下载的功能，在下载大的Object的时候，可以分多次下载。如果下载中断，重启的时候也可以从上次完成的位置开始继续下载。

和简单上传类似，您也需要对该Object有读权限。通过设置参数Range来支持断点续传，对于比较大的Object建议使用该功能。Range的定义可参考HTTP RFC。如果在请求头中使用Range参数，则返回消息中会包含整个文件的长度和此次返回的范围。例如：Content-Range: bytes 0–9/44，表示整个文件长度为44，此次返回的范围为0–9。如果不在范围内，则传送整个文件，并且不在结果中提及Content-Range，返回码为206。

### 查看文件列表

您可以通过 UFile API 中的 PrefixFileList 接口列出您在存储空间（Bucket）中上传的文件（Object）。

查看文件列表的 API 详细信息请参考 [PrefixFileList](https://docs.ucloud.cn/api/ufile-api/prefix_file_list)。

### 删除文件

删除文件即删除上传到存储空间（Bucket）中的文件（Object）。

UFile允许您执行如下删除动作：

* 单个删除：指定某个Object进行删除。

* 自动删除：如果需要删除的Object数目很多，而且删除的Object有一定的规律，比如定期删除某些天之前的Object，或者是要清空整个Bucket，推荐使用生命周期管理来自动删除Object。设置了生命周期规则之后，UFile会根据规则自动删除到期的Object，从而极大减少您发送删除请求的次数，提高删除效率。

注：目前生命周期删除需要提非标需求，请联系技术支持。

