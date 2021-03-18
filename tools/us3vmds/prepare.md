
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

