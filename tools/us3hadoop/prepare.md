# 下载与环境准备

## 下载与安装

### 获取适配器

> US3大数据适配器通过jar包形式提供其功能，US3大数据适配器jar包格式为us3-bigdata-adaptor-${hadoop version}-${adaptor version}.jar；

### 适配器版本

- v1.3.0
  - [hadoop-2.6.0](http://us3-release.cn-bj.ufileos.com/us3-bigdata/adaptor/v1.3.0/us3-bigdata-adaptor-2.6.0-1.3.0.jar)
  - [hadoop-2.8.3](http://us3-release.cn-bj.ufileos.com/us3-bigdata/adaptor/v1.3.0/us3-bigdata-adaptor-2.8.3-1.3.0.jar)
  - [hadoop-2.8.5](http://us3-release.cn-bj.ufileos.com/us3-bigdata/adaptor/v1.3.0/us3-bigdata-adaptor-2.8.5-1.3.0.jar)
  - [hadoop-3.1.1](http://us3-release.cn-bj.ufileos.com/us3-bigdata/adaptor/v1.3.0/us3-bigdata-adaptor-3.1.1-1.3.0.jar)

**如有其他版本Hadoop对接需求，请联系技术支持。**

## 安装适配器

1. 配置各个节点的core-site.xml参数项，参数项参考[快速上手-参数说明](/ufile/tools/us3hadoop/quickaccess?id=参数说明)；
2. 将us3-bigdata-adaptor-${hadoop version}-${adaptor version}.jar拷贝到$HADOOP_HOME/share/hadoop/common/lib/下；

**接入方式具有侵入性，适用于小规模计算分析场景，大数据备份需求场景请参考[快速上手-场景示例](/ufile/tools/us3hadoop/quickaccess?id=场景示例)。**
