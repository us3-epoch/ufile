# 下载与环境准备

## 运行环境

us3fs基于用户态fuse实现，您的机器需要支持fuse。

建议您将us3fs运行在以下环境中：

* linux
  * ceontos 7.0及以上 (可通过`cat /etc/redhat-release`查看)
  * ubuntu 16.04及以上 (可通过`cat /etc/issue`查看)

* 网络环境：us3fs支持在ucloud内网以及互联网环境下使用，在内网环境下，你可以使用内网域名以提升性能和稳定性。

## 下载链接

[下载链接](http://ufile-release.cn-bj.ufileos.com/us3fs%2Fus3fs)

```
curl -o us3fs http://ufile-release.cn-bj.ufileos.com/us3fs%2Fus3fs
```

## 配置账号访问信息

编辑/etc/us3fs/us3fs.conf并增加如下信息(如果没有该目录需要自行创建):

```
yaml
bucket: testzwb
access_key: ************************************
secret_key: ************************************
endpoint: ufile.cn-north-02.ucloud.cn
```

*冒号后有单个空格*

* **bucket**: 桶名，需要和挂载的桶名一致
* **access_key**: 公钥，支持token秘钥和api秘钥两种模式
* **secret_key**: 私钥，支持token秘钥和api秘钥两种模式
* **endpoint**: 访问域名，详见[地域和域名](https://docs.ucloud.cn/ufile/introduction/region)。填写时需要去掉`www.`

当需要在一台机器上挂载多个bucket时，可以通过`--passwd=passwd_file`指定账号信息（默认路径为 /etc/us3fs/us3fs.conf，不需要指定）。

下载us3fs后。使用`chmod +x us3fs`增加可执行权限，如果需要直接执行，可将us3fs移动到/bin目录下。示例：

```
bash
chmod +x us3fs
./us3fs --passwd=passwd_file <bucket> <mountpoint>

# 移动到可执行目录下
mv us3fs /bin/us3fs
us3fs --passwd=passwd_file <bucket> <mountpoint>
```
