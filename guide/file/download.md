


# 下载文件

## 简单下载

简单下载是通过 US3 API 的 GetObject 接口，下载已经上传的文件（Object），Object 下载是使用 HTTP 的 GET 请求来完成的。

* 简单下载的 API 接口详细信息请参见 [GetFile](https://docs.ucloud.cn/api/ufile-api/get_file)。

* Object 的 URL 生成规则请参考 US3 的访问。

* 如果需要使用自定义域名来访问 Object，请参考自定义域名访问 US3。

## 断点续传下载

US3 提供了从 Object 指定的位置开始下载的功能，在下载大的 Object 的时候，可以分多次下载。如果下载中断，重启的时候也可以从上次完成的位置开始继续下载。

和简单上传类似，您也需要对该 Object 有读权限。通过设置参数 Range 来支持断点续传，对于比较大的 Object 建议使用该功能。Range 的定义可参考 HTTP RFC。如果在请求头中使用 Range 参数，则返回消息中会包含整个文件的长度和此次返回的范围。例如`Content-Range: bytes 0–9/44`，表示整个文件长度为 44，此次返回的范围为 0–9。如果不在范围内，则传送整个文件，并且不在结果中提及 Content-Range ，返回码为 206。

## 查看文件列表

您可以通过 US3 API 中的 PrefixFileList 接口列出您在存储空间（Bucket）中上传的文件（Object）。

查看文件列表的 API 详细信息请参考 [PrefixFileList](https://docs.ucloud.cn/api/ufile-api/prefix_file_list)。

## 删除文件

删除文件即删除上传到存储空间（Bucket）中的文件（Object）。

US3 允许您执行如下删除动作：

* 单个删除：指定某个 Object 进行删除。

* 自动删除：如果需要删除的 Object 数目很多，而且删除的 Object 有一定的规律，比如定期删除某些天之前的 Object，或者是要清空整个Bucket，推荐使用生命周期管理来自动删除 Object。设置了生命周期规则之后，US3 会根据规则自动删除到期的 Object，从而极大减少您发送删除请求的次数，提高删除效率。

备注：该功能处于内测阶段，如需使用请联系技术支持。

