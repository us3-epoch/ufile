{{indexmenu_n>1}}

# 上传文件

开发者可以选择以下三种上传类型来完成一个文件的上传过程。

## 普通上传

普通上传是指用户使用对象存储 API中的Put
File方法上传单个File，可以适用在任何一次HTTP请求交互即可完成上传的场景，比如小文件的上传。

## 表单上传

表单上传是一种适用于小文件上传的上传方式，是指用户使用对象存储 API中的Post File请求来完成File的上传。

## 分片上传

分片上传是指将待上传文件分成多个数据块来分别上传，上传完成之后再调用对象存储 API将这些Part组合成一个File。

分片上传基于“分片上传”相关接口完成，即InitMultipartUpload、UploadPart、FinishMultipartUpload。

1、首先，调用InitMultipartUpload，得到此次分片上传的唯一标识uploadId。

<http://docs.ucloud.cn/api/ufile-api/initiate_multipart_upload.html>

2、其次，将文件按4M进行切片，并按其在整个文件中的顺序进行编号。上传每个分片内容时，需要携带uploadId及其编号partNumber。

<http://docs.ucloud.cn/api/ufile-api/upload_part.html>

3、最后，所有分片上传完成后，发送FinishMultipartUpload结束此次分片上传，文件上传成功。

<http://docs.ucloud.cn/api/ufile-api/finish_multipart_upload.html>

(各语言的SDK包中均有封装分片上传方法)

如果是使用内容管理工具filemgr，可直接使用其mput功能，例如：

./filemgr --action mput --bucket demobucket --key largefile --file
./largefile.dat

若中途失败，可反复执行mput，将会自动续传。
