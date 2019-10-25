

# UFile迁移工具

## 介绍
ufile-import是对象存储UFile提供的一款将数据迁移至UFile(Bucket)的工具。您可以将ufile-import部署在本地服务或者云主机上，轻松将您其他云存储的数据迁移到UFile。

### 适用情况

* 阿里云对象存储数据迁移到UFile对象存储
* 七牛云对象存储数据迁移到UFile对象存储
* UFile对象存储不同bucket之前的数据迁移
* 支持s3协议的对象存储迁移到UFile对象存储

### 准备工作
1. 根据需要迁移的文件的总大小，选择硬盘合适的云主机。必须要保证硬盘存储量大于文件迁移数据量,否则可能会因为硬盘大小不够，而造成迁移数据不完整。  

举例：假设您有总量为100G的文件需要迁移，设置的处理文件并发数是40(即ufile-import.json中的"concurrent"参数，可以往下阅读，了解该参数的使用。)，在全部的迁移文件中，单个文件最大大小为1G左右，则您至少需要大小为:40乘以1乘以2=80G,左右大小的硬盘，来缓存下载过程中的临时文件,来保证迁移过程中，有足够的硬盘容量，来缓存临时文件,否则可能会造成迁移文件的不完整

## 安装

### 通用步骤:

####  1. 下载安装包
Linux64位操作系统请下载
下载地址: https://github.com/ufilesdk-dev/ufile-import/archive/master.zip

####  2. 安装程序。

进入下载安装包目录，解压文件. 

    unzip master.zip 
    cd ufile-import-master  
    tar zxvf  ufile-import.tgz 

#### 3. 启动redis服务。

服务依赖于redis服务，安装包中已经还有redis服务的相关配置，直接启动即可。  

     1 cd ufile-import  
     2 cd redis 
     3 ./start.sh 

可以通过执行 `./ps.sh`命令来查看redis服务状态，redis服务正常启动，状态如下:

     root      5318     1  0 14:50 pts/0    00:00:15 ./redis-server 127.0.0.1:6379

工具使用详情参照[UFile迁移工具使用说明](https://github.com/ufilesdk-dev/ufile-import)
