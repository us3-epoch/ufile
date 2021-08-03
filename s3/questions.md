# AWS S3 协议常见问题

### S3Browser上传超大文件(78G以上)报错400,错误信息显示分片Size=16.00MB

#### 问题原因：

​        s3分片数限制为1万条，设置分片为8M是，只能满足78G左右以下文件的上传，如果文件大小超过78G,会自动调整分片大小，保证分片数小于1万条。目前us3后端s3协议只支持8M的分片，如果分片大小不对，会返回400错误。

#### 解决方案：

- 使用内测版US3Browser工具进行上传(s3协议),有需要使用，请联系技术支持获取使用权限
- 使用US3CLI工具进行上传(us3协议)。使用方式参考 [US3CLI 工具简介](https://docs.ucloud.cn/ufile/tools/us3cli/introduction)

### Rclone如何使用

参考以下配置进行填写:

```
type = s3
provider = Other
env_auth = false
access_key_id = TOKEN_7468973e-xxxxxxxxxxxxxxxxxxxxx
secret_access_key = 9a632eb8-xxxxxxxxxxxxxxxxxxx
endpoint = http://internal.s3-cn-bj.ufileos.com
location_constraint = cn-bj
acl = private
bucket_acl = private
chunk_size = 8M
copy_cutoff = 500G
disable_checksum = true
session_token = xxxxxxxxx
force_path_style = false
upload_cutoff = 16M
```

注意：

1. 请使用s3协议域名
2. 分片大小只支持8MB
3. 以上只是填写示例，具体条目需要用户自行修改

