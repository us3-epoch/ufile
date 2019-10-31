# 数据迁移说明



数据迁移说明文档将介绍从其他源站迁移数据到UFile的方式。

## UFile镜像回源功能

用户在UFile创建存储空间后，通过配置存储空间的回源配置，UFile将在接收到访问请求时，会先在UFile本地查找文件，如果文件不存在，则会回源到用户指定的源站获取文件，并且在UFile本地存储一份。下一次访问同一个文件时就会直接从UFile本地直接返回给用户。

具体设置方法请参考：
[设置回源规则](https://docs.ucloud.cn/storage_cdn/ufile/guide/mirror)

优点：

客户的请求可以快速无缝的切换到UFile

缺点：

1.部分未访问到的文件无法迁移到UFIle  

2.访问UFile不存在的文件需要回源，访问延时会增加

## UFile数据迁移工具

### 介绍
ufile-import是对象存储UFile提供的一款将数据迁移至UFile(Bucket)的工具。您可以将ufile-import部署在本地服务或者云主机上，轻松将您其他云存储的数据迁移到UFile。

### 适用情况
阿里云对象存储数据迁移到UFile对象存储
七牛云对象存储数据迁移到UFile对象存储
UFile对象存储不同bucket之前的数据迁移
支持s3协议的对象存储迁移到UFile对象存储

详情请参照[管理工具-数据迁移工具](https://docs.ucloud.cn/storage_cdn/ufile/tools/tools/ufile_import)
