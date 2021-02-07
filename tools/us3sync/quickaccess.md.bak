# 快速上手

- [配置管理](#配置管理)
- [创建存储桶](#创建存储桶)
- [上传文件或文件夹](#上传文件或文件夹)
- [下载文件或文件夹](#下载文件或文件夹)
- [拷贝文件或文件夹](#拷贝文件或文件夹)
- [删除文件或文件夹](#删除文件或文件夹)
- [查询文件基本信息](#查询文件基本信息)
- [文件基本信息修改](#文件基本信息修改)

## 配置管理

此处主要介绍us3cli工具配置方式以及常见的使用场景，us3cli工具支持多种配置生成方式，对于不同配置项，支持配置项之间的切换，配置项删除，更新，查看。对于单个配置项，支持配置内容加密（公私钥）、HTTPS、代理。

配置项：一个配置项代表一个不同权限的配置文件，便于不同操作权限区分管理

- [常用配置](#常用配置)
  - [配置项创建](#配置项创建)
  - [配置项管理](#配置项管理)
- [临时配置](#临时配置)

​        配置方式根据是否保存配置文件做区分，分为常用配置和临时配置，常用配置可进行更新，切换，查看等操作，临时配置作为参数在其他命令运行时输入，表示当前配置参数只在当前命令运行时生效。

### 常用配置

命令格式：

```
us3cli config [--ls][--su <configname>][--rm <configname>][--cat <configname>][configname]
              [--accesskey <API/Token公钥>][--secretkey <API/Token私钥>][--endpoint <访问域名>]
```

常用配置创建方式分为两种：交互式配置和一键配置,命令格式相同，使用方式不同

#### 配置项创建

1. 交互式配置

使用示例：

```
# ./us3cli config
输入当前配置项名称: config1
创建配置项
请输入API公钥[当前:]: TOKEN_13be86*********
请输入API私钥[当前:]: BAtrQO8LYdgve1HS_benbK-MXNTl3**********
地区列表：
No.     RegionName      Region      
0       北京            cn-bj       
1       上海二          cn-sh2      
2       广州            cn-gd       
3       香港            hk          
4       洛杉矶          us-ca       
5       新加坡          sg          
6       雅加达          idn-jakarta 
7       台北            tw-tp       
8       拉各斯          afr-nigeria 
9       圣保罗          bra-saopaulo
10      迪拜            uae-dubai   
11      法兰克福        ge-fra      
12      胡志明市        vn-sng      
13      华盛顿          us-ws       
14      孟买            ind-mumbai  
15      首尔            kr-seoul   
请输入region编号: 0
内外网列表：
No.     Network
0       外网   
1       内网   
请选择或输入内外网编号：0
您选择的endpoint是：[cn-bj.ufileos.com],[当前:]，请输入回车确认或自定义endponit：

当前最终配置:
ConfigName: config1
AccessKey: TOKEN_13be86*********
SecretKey: BAtrQO8LYdgve1HS_benbK-MXNTl3**********
Endpoint: cn-bj.ufileos.com

是否使用该配置作为默认配置(当前默认配置为：<configtest>)(y or n)? y
```

2. 一键配置

使用示例：

```
./us3cli config config1 --accesskey TOKEN_13be86********* --secretkey BAtrQO8LYdgve1HS_benbK-MXNTl3********** --endpoint cn-bj.ufileos.com
```

#### 配置项管理

```
#切换默认配置
./us3cli config --su config1

#删除指定配置（只支持指定配置项名称删除，不支持指定路径）
./us3cli config --rm config1

#配置项内容查看，包含accesskey、secretkey、endpoint
./us3cli config --cat config1

#查看当前所有配置项列表，默认配置会标明 "(default)",Authority表示当前配置是Token密钥类型还是API密钥类型(只判断类型，不进行权限验证)
./us3cli config --ls
```

### 临时配置

临时配置可以通过配置名、配置文件路径、配置项内容使用，具体示例如下:

```
#1.通过配置名指定临时配置
./us3cli ls us3://bucket1 --config config2

#2.通过配置文件路径指定临时配置
./us3cli ls us3://bucket1 --config ~/go/src/userconfig.yaml

#3.通过配置项内容直接指定临时配置
./us3cli ls us3://bucket1 --accesskey "xxxxxx" --secretkey "xxxxxx" --endpoint "xxxxxx"
```

注:当前版本支持自定义配置文件，但只支持与工具自动生成配置文件内容相同的配置文件

自定义配置文件内容如下,具体填写说明见常用命令页config命令介绍

```
accesskey: "user accesskey"
secretkey: "user secretkey"
endpoint: "ufile.cn-north-02.ucloud.cn"
```

## 创建存储桶

### 交互式创建

```
命令格式:
us3cli mb us3://<bucketname>

使用示例：
# ./us3cli mb us3://bucketTest

请输入要创建bucket的权限类型acl(private/public,默认为private):private
地区列表：
No.     RegionName      Region      
0       北京            cn-bj       
1       上海二          cn-sh2      
2       香港            hk          
3       广东            cn-gd       
4       洛杉矶          us-ca       
5       新加坡          sg          
6       首尔            kr-seoul    
7       尼日利亚        afr-nigeria 
8       台北            tw-tp       
9       圣保罗          bra-saopaulo
10      迪拜            uae-dubai   
11      越南            vn-sng      
12      孟买            ind-mumbai  
13      华盛顿          us-ws       
14      法兰克福        ge-fra      
请输入要创建bucket地区编号或地区代码(默认为北京:cn-bj):0
Region: cn-bj
当前账号下项目信息如下：
No.     ProjectName     ProjectId 
1       Default         org-orcwsj
请输入要创建bucket的项目编号:1
Number: 1
ProjectID: org-orcwsj
2020-11-24 17:52:56.973 INFO Make bucket [ bucketTest ] success
```

### 一键创建

```
#命令格式：
us3cli mb us3://<bucketname> --projectid <projectid> --region <region> --acl <acl>

#使用示例：
./us3cli mb us3://buckettest --projectid org-test --region cn-bj --acl public
```

## 上传文件或文件夹

### 上传单个文件

```
#命令格式：
#普通文件
us3cli cp <local filename> us3://<bucketname>/<us3key>
#流式文件
us3cli rcat us3://<bucketname>/<us3key>

#使用示例：
#普通上传文件，不指定任何参数
./us3cli cp ./test.txt us3://buckettest/test.txt
#指定存储类型为IA上传 (不区分大小写)
./us3cli cp ./test.txt us3://buckettest/test.txt --storageclass IA
#指定并发数，设置并发数为10(文件大于64MB时才会有效果,因为64MB以上大文件才会使用分片上传)
./us3cli cp ./test.txt us3://buckettest/test.txt --parallel 10
#上传文件并限速50MB/s以下
./us3cli cp ./test.txt us3://buckettest/test.txt --speedlimit 50MB
#将本地文件test.txt cat到标准输入，再上传至us3存储桶内
cat test.txt | ./us3cli rcat us3://buckettest/test.txt
#流式上传并指定失败重试次数为10，设置并发数为10
cat test.txt | ./us3cli rcat us3://buckettest/test.txt --retrycount 10 --parallel 10
```

### 上传文件夹

```
#命令格式：
us3cli cp -r <localdir> us3://<bucketname>/<us3key>

#使用示例:
#普通上传文件夹
./us3cli cp -r ./testdir us3://buckettest/us3dir
#上传后缀名为“.txt"的文件
./us3cli cp -r ./testdir us3://buckettest/us3dir --include "*txt"
#上传文件名不包含test的文件
./us3cli cp -r ./testdir us3://buckettest/us3dir --exclude "*test*"
```

### 增量上传文件夹

增量上传文件夹：对比本地文件夹和us3对应的文件夹，忽略已上传的文件，将未上传过的文件上传到us3文件夹内

```
#命令格式:
us3cli sync <localdir> us3://<bucketname>/<us3key>

#使用示例:
#遍历本地文件夹，以本地缓存为标准进行同步，如果文件修改时间晚于本地缓存保存的时间，或者未保存过，则上传文件，否则跳过
./us3cli sync ./testdir us3://buckettest/us3dir
#遍历本地文件夹，以本地缓存为标准进行同步，如果文件etag和本地缓存保存的etag不同，则上传文件，否则跳过
./us3cli sync ./testdir us3://buckettest/us3dir --ruler etag
#遍历本地文件夹，对比本地和us3所有文件，如果文件修改时间晚于us3文件的修改时间，则上传文件，否则跳过
#如果存在文件在us3的目录下存在，但在本地不存在，则删除us3中的该文件（该删除操作会默认询问，目前不提供强制删除功能）
./us3cli sync ./testdir us3://buckettest/us3dir --mode remote
#遍历本地文件夹，对比本地和us3所有文件，如果文件etag和us3同一文件的etag不同，则上传文件，否则跳过
./us3cli sync ./testdir us3://buckettest/us3dir --mode remote --ruler etag
```

## 下载文件或文件夹

### 下载单个文件

```
#命令格式：
#普通下载
us3cli cp  us3://<bucketname>/<us3key> <local filename>
#流式下载
us3cli cat us3://<bucketname>/<us3key>


#使用示例：
#普通下载单个文件
./us3cli cp us3://buckettest/test.txt ./test.txt

#下载并限速50MB/s
./us3cli cp us3://buckettest/test.txt ./test.txt --speedlimit 50MB/s

#下载文件，每个分片大小指定为8M(不区分大小写，并且默认大小为4M，最小值为4M)
./us3cli cp us3://buckettest/test.txt ./test.txt --partsize 4M

#流式文件下载(文件会写入到标准输入)
./us3cli cat us3://buckettest/test.txt

#流式下载并指定并发数，重试次数为10
./us3cli cat us3://buckettest/test.txt --parallel 10 --retrycount 10
```

### 下载文件夹

```
#命令格式：
us3cli cp -r  us3://<bucketname>/<us3key> <localdir>


#使用示例:
#普通下载文件夹
./us3cli cp -r  us3://buckettest/us3dir ./testdir

#下载文件夹并指定并发数为20
./us3cli cp -r us3://buckettest/us3dir ./testdir --parallel 20

#下载文件夹并限速为100MB/s
./us3cli cp -r us3://buckettest/us3dir ./testdir --speedlimit 100MB
```

## 拷贝文件或文件夹

```
#命令格式
us3cli cp us3://<bucketname>/<us3key>


#使用示例
#将文件从bucket1拷贝至bucket2中（两个bucket必须在同一地域中，如需要不同地域的数据复制，则需要使用跨区域复制功能）
./us3cli cp us3://bucket1/test.txt us3://bucket2/test.txt

#将文件夹从bucket1拷贝到bucket2中
./us3cli cp -r us3://bucket1/test us3://bucket2/test

#将bucket1中test文件夹中以".txt"结尾的文件拷贝到bucket2的test文件夹中
./us3cli cp -r us3://bucket1/test us3://bucket2/test --include "*.txt"
```

## 删除文件或文件夹

### 删除单个文件

```
#命令格式：
us3cli rm  us3://<bucketname>/<us3key>


#使用示例：
#删除文件
./us3cli rm us3://buckettest/test.txt

#强制删除
./us3cli rm -f us3://buckettest/test.txt
```

### 删除文件夹

```
#命令格式：
us3cli rm  -r us3://<bucketname>/<us3key>

#使用示例：
#删除文件夹
./us3cli rm -r us3://buckettest/test

#强制删除整个test文件夹
./us3cli rm -r -f us3://buckettest/test

#删除test文件夹下不含有test的文件
./us3cli rm -r -f us3://buckettest/test --exclude "*test*"

#设置并发数为10，强制清空当前buckettest存储桶
./us3cli rm -r -f us3://buckettest --parallel 10 

#删除文件夹，限制请求数量为每秒10个
./us3cli rm -r -f us3://buckettest/test --qps 10
```

## 查询文件基本信息

```
#命令格式
us3cli stat us3://<bucketname>/<us3key>

#使用示例
./us3cli stat us3://buckettest/test.txt
```

## 获取文件列表

```
#命令格式
us3cli ls us3://<bucketname>[/us3key]

#使用示例
./us3cli ls us3://buckettest

#拉取buckettest中的文件，只显示10个
./us3cli ls us3://buckettest --limit 10

#拉取buckettest中的文件，并以非目录形式展示
#非目录形式：目录下所有的文件都显示完整路径，子目录下的文件也会列出
./us3cli ls us3://buckettest --flat

#列取buckettest中的文件，并展示是否已取回，以及取回时间
#数据取回：指归档类型的数据短暂激活，可以下载
./us3cli ls us3://buckettest --restore
```

## 文件基本信息修改

```
#命令格式
us3cli modify us3://<bucketname>/us3key

#使用示例
#修改文件mimetype为xxx/yyy
./us3cli modify us3://buckettest/test.txt --mimetype xxx/yyy

#为文件增加metadata  key为 "name"  value为 "us3cli"
./us3cli modify us3://buckettest/test.txt --metadata name=us3cli

#清空当前文件的metadata
./us3cli modify us3://buckettest/test.txt --metadata "" --replace

#修改文件的存储类型为ARCHIVE(归档类型)
./us3cli modify us3://buckettest/test.txt --storageclass ARCHIVE
```

