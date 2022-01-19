
# 下载与环境准备

## 下载与安装

### 获取可执行文件

> us3vmds通过二进制可执行文件提供，文件格式为us3vmds-${version}-${OS}；

#### 公测版本

- [us3vmds-v0.0.1-linux](http://us3-release.cn-bj.ufileos.com/us3-bigdata/us3-vmds/v0.0.1/us3vmds-v0.0.1-linux)
- [us3vmds-v0.0.1-macOS](http://us3-release.cn-bj.ufileos.com/us3-bigdata/us3-vmds/v0.0.1/us3vmds-v0.0.1-mac)

#### 正式版本

> 待发布...

**注意：对应的US3适配版本需要>=v1.0.2**

### 安装可执行文件

1. 下载可执行文件并移动到/usr/bin目录下，如`mv us3vmds-v0.0.1 /usr/bin`;
2. 为最新的us3vmds建立软链接，如`ln -s /usr/bin/us3vmds-v0.0.1 /usr/bin/us3vmds`;
3. us3vmds配置参数，参考以下【使用说明】;
4. us3适配器配置参数，参考文档[US3Hadoop 大数据适配器工具](https://docs.ucloud.cn/ufile/tools/us3hadoop/quickaccess?id=%e5%8f%82%e6%95%b0%e8%af%b4%e6%98%8e) 中`fs.us3.metadata.use`和`fs.us3.metadata.host`参数项配置说明；

**建议: 部署节点选择NameNode节点**

## 高可用集群部署

**注意： 对应的US3适配器版本需要>=v1.3.0，vmds本身的版本需要>=v0.1.0，另外由于容器需要去zookeeper上获取vmds节点信息，高可用部署会造成一定的性能损失，与容器数目相关，与数据量关系不大，通常是分钟级别的性能下降。**

### 步骤：

1. 根据[上文](https://docs.ucloud.cn/ufile/tools/us3vmds/prepare?id=%e4%b8%8b%e8%bd%bd%e4%b8%8e%e5%ae%89%e8%a3%85)配置好相应的jar包，以及相应的core-site.xml配置文件。US3Hadoop大数据适配器默认会根据如下优先级寻找vmds服务节点信息：

   > 1. 在core-site.xml文件中配置的fs.us3.metadata.host参数，直接使用standalone单节点vmds服务。
   >
   > 2. 根据在core-site.xml文件中配置的fs.us3.metadata.zookeeper.addrs获取zookeeper信息，从zookeeper中获取vmds服务主节点信息。
   > 3. 根据core-site.xml文件中的ha.zookeeper.quorum参数，使用hadoop中自带的zookeeper，并从其中获取vmds服务主节点信息。

   这里我们直接使用hadoop中自带的zookeeper。

2. 启动vmds服务。启动参数之中增加-z选项，并附加zookeeper的host:port信息(如果您不提供port，则会使用默认的zookeeper端口2181)。如果您有多台zookeeper则需要重复使用-z选项。例如:

   ```shell
   ./us3vmds- start -z 10.0.0.1 -z 10.0.0.2 -z 10.0.0.3
   ```

   这里的zookeeper节点信息可以在core-site.xml文件中使用ha.zookeeper.quorum参数提供的地址。注意如果您不提供-z选项，则zookeeper将使用单节点模式。

3. 到这里高可用的vmds服务集群就启动好了，您可以使用hadoop fs相关命令进行测试。

