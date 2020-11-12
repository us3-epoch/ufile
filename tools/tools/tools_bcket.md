

# 空间管理工具

空间管理工具可帮助用户获取存储相关信息并进行空间管理，支持创建存储空间、删除存储空间、存储空间信息查看与修改和存储空间文件列表查询。

### 下载链接

[bucketmgr-win32](http://tools.ufile.ucloud.com.cn/bucketmgr-win32.zip)

[bucketmgr-win64](http://tools.ufile.ucloud.com.cn/bucketmgr-win64.zip)

[bucketmgr-linux64](http://tools.ufile.ucloud.com.cn/bucketmgr-linux64.tar.gz)

[bucketmgr-mac](http://tools.ufile.ucloud.com.cn/bucketmgr-mac.tar.gz)

### 使用说明

Linux/Mac用户请在terminal执行，Windows用户请在cmd终端执行。使用前请修改当前目录下的配置文件config.cfg，把API密钥加入到配置项：

```
{
   "public_key" : "paste your public key here",
   "private_key" : "paste your private key here",
   "api_host" : "api.spark.ucloud.cn"  
}
```

备注：API 密钥可以在控制台的“API 密钥”页面获得。将 public\_key 和 private\_key 分别填入 config.cfg 文件的对应位置，客户端工具通过此密钥完成鉴权。请妥善保管好 API 密钥，避免泄露。

#### 创建存储空间

创建存储空间建议使用控制台。

```
./bucketmgr --action CreateBucket --bucket bucketname --type public --region region --project-id project-id
参数说明:
--bucket: 需要创建的存储空间名
--type: 需要创建的空间类型，公开空间（public）或私有空间（private）
--region: 需要创建的存储空间所属地区，默认北京
--project-id: 需要创建的空间所属项目组ID
```

示例：

在org-mutvtj项目下创建uclouddemo存储空间：

```
./bucketmgr --action CreateBucket --bucket uclouddemo --type public --region cn-bj --project-id org-mutvtj
```

#### 删除存储空间

删除存储空间建议使用控制台。

```
./bucketmgr --action DeleteBucket --bucket bucketname --project-id project-id
参数说明:
--bucket: 需要删除的存储空间名
--project-id: 需要删除的空间所属项目组ID
```

#### 获取空间信息

```
./bucketmgr --action DescribeBucket --project-id project-id
参数说明:
--bucket: 需要查询的存储空间名
--project-id: 需要获取的空间所属项目组ID
```

#### 获取文件列表

```
./bucketmgr --action GetFileList --bucket bucketname --limit limit --pattern pattern --format format
参数说明:
--bucket: 需要拉取列表的存储空间名
--limit : 需要查询的记录数（默认取全部）
--pattern: 需要查询记录的模板，支持POSIX正则表达式
--format: 格式化查询记录
```
