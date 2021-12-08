# 下载与环境准备

## 运行环境

US3FS基于Linux下fuse和Windows平台下winfsp实现，您的机器需要支持fuse或winfsp。

建议您将US3FS运行在以下环境中：

* Linux
  * ceontos 7.0及以上 (可通过`cat /etc/redhat-release`查看)
  * ubuntu 16.04及以上 (可通过`cat /etc/issue`查看)
* Windows
  * 下载[WinFsp Installer](https://github.com/billziss-gh/winfsp/releases/download/v1.9/winfsp-1.9.21096.msi)
  * 根据[官方说明](http://www.secfs.net/winfsp/rel/) 进行安装

> US3FS支持在UCloud内网以及互联网环境下使用，在内网环境下，你可以使用内网域名以提升性能和稳定性。

## 下载链接

[Linux下载链接](https://ufile-release.cn-bj.ufileos.com/us3fs/us3fs) 或

```shell
curl -o us3fs https://ufile-release.cn-bj.ufileos.com/us3fs/us3fs
```

[Windows下载链接](https://ufile-release.cn-bj.ufileos.com/us3fs/us3fs.exe)

## 配置账号访问信息

### Linux

编辑/etc/us3fs/us3fs.yaml并增加如下信息（如果没有该目录需要自行创建）：

```
bucket: testzwb
access_key: ************************************
secret_key: ************************************
endpoint: ufile.cn-north-02.ucloud.cn
hosts: []
```

*冒号后有单个空格*

* **bucket**: 存储空间名，需要和挂载的存储空间名一致
* **access_key**: 公钥，支持token秘钥和api秘钥两种模式
* **secret_key**: 私钥，支持token秘钥和api秘钥两种模式
* **endpoint**: 访问域名，详见[地域和域名](https://docs.ucloud.cn/ufile/introduction/region)。填写域名为地域域名，并非具体的存储空间域名。
* **hosts**: 指定访问点IP列表，不会走DNS解析逻辑获取US3接入层IP。如果指定个数小于3个不会生效。 如: `hosts: [10.9.254.190, 117.50.123.23, 117.50.123.29, 117.50.123.8]`

> hosts指定的IP列表，在遇到异常(网络不可达)IP节点时会在5s检测周期内自动标记剔除，新的请求不受影响，但已经发起的请求且使用异常网络的链接，由于TCP采用退避指数重试算法，默认重试次数为15次，所以最坏情况要到15min左右才能检测异常，建议在使用该参数时，修改Linux参数net.ipv4.tcp.retries2(或修改系统文件/proc/sys/net/ipv4/tcp_retries2)为6，可使如网络不可达异常在25s左右能检测到，从而剔除已建立异常链接；此外由于目前链接保活探测逻辑会占用一定量的文件描述符，建议调整系统设置，请参考**场景问题** 中的**系统日志出现too many open file**问题项解决。

当需要在一台机器上挂载多个Bucket时，可以通过`--passwd=passwd_file`指定账号信息（默认路径为 /etc/us3fs/us3fs.yaml，不需要指定）。

下载US3FS后。使用`chmod +x us3fs`增加可执行权限，如果需要直接执行，可将us3fs移动到/bin目录下。示例：

```shell
chmod +x us3fs
./us3fs --passwd=passwd_file <bucket> <mountpoint>

# 移动到可执行目录下
mv us3fs /bin/us3fs
us3fs --passwd=passwd_file <bucket> <mountpoint>
```

### Windows

配置信息内同linux，配置路径自定义。

下载好可执行文件后移动到us3fs的工作目录(自定义)，然后通过按键`windows`+`R`打开运行窗口，输入`cmd`进入命令行工具界面(后续支持图形化界面)，进入可执行文件`us3fs.exe` 所在路径。示例:

```shell
# 进入可执行文件所在盘符，这里是D盘
C:\Users\Administrator> D:
# 进入可执行文件所在路径
D:\>cd us3fs
D:\us3fs>dir
驱动器 D 中的卷没有标签。
卷的序列号是 5CAF-F66B

 D:\us3fs 的目录
2021/09/09  21:16    <DIR>          .
2021/09/09  21:16    <DIR>          ..
2021/09/09  19:13        19,475,146 us3fs.exe
2021/08/26  11:29               157 us3fs.yaml
               2 个文件     19,475,303 字节
               2 个目录 213,768,716,288 可用字节
# 进行挂载操作
# * 这里挂载到x盘，并且指定uid,gid为0的用户，日志级别为debug，预读窗口为32MiB，挂载的US3桶名为rickwu
D:\us3fs>us3fs.exe --passwd=us3fs.yaml -o debug --uid=0 --gid=0 --level=debug --readahead=32m rickwu x:

```

> *注意目前Windows下挂载只能前台挂载*

 
