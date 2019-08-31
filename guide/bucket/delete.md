{{indexmenu_n>1}}

# 删除存储空间

您可以通过 UFile API 的 DeleteBucket 接口删除您创建的存储空间。
删除存储空间的 API 详细信息可参考[DeleteBucket](https://docs.ucloud.cn/api/ufile-api/delete_bucket)

    如果存储空间不为空（存储空间中有文件或者是尚未完成的分片上传），则存储空间无法删除，必须删除存储空间中的所有文件和未完成的分片文件后，存储空间才能成功删除。如果想删除存储空间内部所有的文件，推荐使用生命周期管理。

