# 常用命令



| 操作           | 命令                | 描述                                                         |
| -------------- | ------------------- | ------------------------------------------------------------ |
| 配置管理       | [config](#config)   | 管理上传所需的公私钥及endpoint等信息，包括配置项的创建、修改、删除、更新、切换 |
| 创建存储桶     | [mb](#mb)           | 创建存储桶                                                   |
| 删除存储桶     | [rb](#rb)           | 删除存储桶（存储桶为空）                                     |
| 查看存储桶信息 | [stat](#stat)       | 查看bucket的元数据信息                                       |
| 查看存储量     | [du](#du)           | 查看bucket的存储量（标准、低频、归档）                       |
| 普通上传       | [cp](#cp)           | 将本地文件或目录上传到存储桶中                               |
| 增量上传       | [sync](#sync)       | 增量上传目录到存储桶中                                       |
| 流式上传       | [rcat](#rcat)       | 将流式文件上传到存储桶中                                     |
| 创建目录       | [mkdir](#mkdir)     | 在us3存储空间中创建一个空目录                                |
| 普通下载       | [cp](#cp)           | 将存储桶中的文件或目录下载到本地                             |
| 流式下载       | [cat](#cat)         | 将存储桶中的数据下载并写入到标准输入中                       |
| 拷贝           | [cp](#cp)           | 将一个存储桶中的文件拷贝到另一个存储桶中（同一地域）         |
| 移动           | [mv](#mv)           | 将文件或目录移动到其他目录下（同一存储同内）                 |
| 删除           | [rm](#rm)           | 删除存储桶中的文件或目录                                     |
| 列表           | [ls](#ls)           | 列出US3存储桶列表或US3存储桶中的文件列表                     |
| 获取下载URL    | [sign](#sign)       | 获取存储桶中文件的下载链接                                   |
| 查看元数据     | [stat](#stat)       | 查看存储桶中文件的元数据信息                                 |
| 修改元数据     | [modify](#modify)   | 修改存储桶中文件的存储类型、MIMETYPE、MetaData               |
| 归档数据取回   | [restore](#restore) | 将归档类型的文件激活为可下载状态                             |
| 数据完整性校验 | [etag](#etag)       | 查看本地文件、标准输出、US3存储桶的文件etag                  |
| 版本更新       | [update](#update)   | 更新工具版本                                                 |
| 版本特性       | [version](#version) | 查看工具版本特性                                             |

## config

config命令用于管理配置文件。

### 命令格式

```
us3cli config  [--ls][--su <configname>][--rm <configname>][--cat <configname>][configname]
			   [--accesskey <API/Token公钥>][--secretkey <API/Token私钥>][--endpoint <访问域名>]
```

### 参数说明

```
-a,--accesskey:用于访问us3的API密钥或Token公钥
-s,--secretkey:用于访问us3的API私钥或Token私钥
-e,--endpoint:固定域名，可在地域和域名页查看
   --cat:打印指定配置项内容
   --ls:列出当前所有配置项
   --rm:删除指定配置项
   --su:切换指定配置为默认配置
```

配置文件内容说明：

| 配置项    | 说明                 | 填写说明                                                     |
| --------- | -------------------- | ------------------------------------------------------------ |
| AccessKey | 用于鉴权的bucket公钥 | [API公钥](https://console.ucloud.cn/uapi/apikey) 、[Token公钥](https://console.ucloud.cn/ufile/token) |
| SecretKey | 用于鉴权的bucket私钥 | [API私钥](https://console.ucloud.cn/uapi/apikey) 、[Token私钥](https://console.ucloud.cn/ufile/token) |
| Endpoint  | 外网或内网域名       | [地域和域名](https://docs.ucloud.cn/ufile/introduction/region) |

自定义配置文件格式，填写说明同上：

```
accesskey: "user accesskey"
secretkey: "user secretkey"
endpoint: "ufile.cn-north-02.ucloud.cn"
```

### 使用示例

1.交互式配置

- 创建配置项

```
./us3cli config

请输入当前配置项名称: config1
创建配置项
请输入API/Token公钥[当前:]: TOKEN_13be86*********
请输入API/Token私钥[当前:]: BAtrQO8LYdgve1HS_benbK-MXNTl3**********
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

注意：首次创建的配置文件时会自动将该配置作为默认配置

- 列出配置项列表

说明：

1.Default标识表示该配置项是当前的默认配置

2.Authority表示权限分类，只用于快速区分Token和API密钥格式，不保证内容准确

```
./us3cli config --ls

ConfigName              ModTime                 FilePath                         Authority                 
config1  (Default)      2020-09-21 14:18:50     /root/.us3cliconfig/config1      Token       
config2                 2020-09-21 14:18:50     /root/.us3cliconfig/config2      Token     
us3cli                  2020-09-16 10:36:00     /root/.us3cliconfig/us3cli       APIKey
```

- 切换配置项

```
./us3cli config --su config2
```

- 删除配置项

```
./us3cli config --rm config1
```

注意：以下所有命令的（y or n）选项规则均不区分大小写，输入yes或y表示确认，其他选项均表示取消

- 打印配置项

```us3cli config --cat config2
./us3cli config --cat config2

AccessKey: TOKEN_13be86*********
SecretKey: BAtrQO8LYdgve1HS_benbK-MXNTl3**********
Endpoint: cn-bj.ufileos.com
```

2.非交互式配置

```
./us3cli config config3 --accesskey TOKEN_AAGASGAZVZV**** --secretkey USAsflmTAAF****** --endpoint cn-bj.ufileos.com
Configuration file [ config3 ] has been updated
```

3.临时使用（对其他命令生效）

- 上传文件时临时使用配置项config3

```
./us3cli cp test.txt us3://bucket1 --config config3
```

- 上传文件时临时使用配置文件 /home/ubuntu/myconfig1

```
./us3cli cp test.txt us3://bucket1 --config /home/ubuntu/myconfig1
```

- 上传文件时使用自定义配置内容

```
./us3cli cp test.txt us3://bucket1 --accesskey LTAI4G3t3BTza47xxxxxxxxxx --secretkey gznFs9daMtKmUaTq9xpxxxxxxxxxxxxx --endpoint cn-bj.ufileos.com
```

## mb

该命令用于创建存储桶

### 命令格式

```
us3cli mb us3://<桶名字>  [--acl <权限类型>][--region <桶所在地区>][--projectid <项目ID>]
```

### 参数说明

```
      --accesskey <string>   :用于访问us3的API公钥或Token公钥
  -a, --acl <string>         :权限类型，可以设置为private、public，默认为private私有(不区分大小写)
      --config <string>      :当前命令临时指定配置名/配置文件路径
      --endpoint <string>    :固定域名，可通过地域和域名页查看,如：cn-bj.ufileos.com
  -h, --help                 :当前命令使用说明
      --projectid <string>	 :项目ID，当前bucket属项目ID，默认为Default
  -r, --region <string>      :桶所在地区，可查看地域信息，默认地区为北京
      --secretkey <string>   :用于访问us3的API私钥或Token私钥
```

本命令提供命令输入和交互式输入二选一的操作，命令输入参数，就会自动跳过交互式输入。

### 使用示例

- 交互式创建bucket

```
./us3cli mb us3://us3cli-test

请输入要创建bucket的权限类型acl(private/public,默认为private):public
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
当前账号下业务组分组信息如下：
No.     ProjectName     ProjectId 
1       Default         org-orcwsj
请输入要bucket的项目编号:1
Number: 1
ProjectID: org-orcwsj
Make bucket [ us3cli-test ] success
```

- 非交互式创建bucket,输入acl，region以及projectid信息，acl,region是必填项，projectid可不填

```
./us3cli mb us3://us3cli-test --acl private --region cn-bj --projectid org-orcwsy
```

注意：当前版本还不支持单独展示projectid，若您的bucket不在默认项目下，bucket操作都需要加上--projectid参数，请创建bucket时自行记录projectid，或登录控制台查看。


## rb

本命令用于删除存储桶

### 命令格式

```
us3cli rb us3://<桶名字> [--projectid <项目ID>][--force]
```

### 参数说明

```
      --accesskey <string>     :用于访问us3的API公钥或Token公钥
      --config <string>        :当前命令临时指定配置名/配置文件路径
      --endpoint <string>      :固定域名，可通过地域和域名页查看
  -f, --force                  :强制删除
  -h, --help                   :当前命令使用说明
      --projectid <string>	   :指定当前bucket所在projectid，可以在控制台查看，默认项目可忽略
      --secretkey <string>     :用于访问us3的API私钥或Token私钥
```

### 使用示例

- 删除存储桶:存储桶必须为空，否则无法删除

```
./us3cli rb us3://bucket1
The bucket [bucket1] is being deleted, continue(y or n)? y
Delete bucket [bucket1] success
```

## stat

本命令用于查看存储桶或文件信息

### 命令格式

```
us3cli stat us3://<桶名字>[/<文件key>][--projectid <项目ID>]
```

### 参数说明

```
      --accesskey <string>   :用于访问us3的API公钥或Token公钥
      --config <string>      :当前命令临时指定配置名/配置文件路径
      --endpoint <string>    :固定域名，可通过地域和域名页查看
  -h, --help                 :当前命令使用说明
      --projectid <string>   :指定当前bucket所在projectid，可以在控制台查看，默认项目可忽略
```

### 使用示例

- 查看默认项目下存储桶bucket1的基本信息

```
./us3cli stat us3://bucket1

BucketName:     bucket1             
Region:         cn-bj              
BucketId:       ufile-dpgjzcn     
Type:           public             
CreateTime:     2020-09-15 18:17:24
ModifyTime:     2020-09-15 18:17:24
```

- 查看us3://bucket1/test.txt的文件信息

```
./us3cli stat us3://bucket1/aaa.txt

Name:                   aaa.txt           
X-Ufile-Create-Time:    Fri, 18 Sep 2020 10:09:05 GMT     
X-Ufile-Storage-Class:  STANDARD                          
Server:                 nginx/1.11.1                      
Date:                   Mon, 21 Sep 2020 11:17:56 GMT     
Content-Type:           application/octet-stream          
Accept-Ranges:          bytes                             
Etag:                   "AQAAAEpjpDD8COEdGg3uOeLfsR_ddQgc"
Content-Length:         4298                              
Last-Modified:          Fri, 18 Sep 2020 10:09:05 GMT     
Vary:                   Origin           
```

## du

该命令用于获取指定存储桶(Bucket)各存储类型的存储量大小，以及总存储量

注意：存储量结算时间为当前日期前一天0:00

### 命令格式

```
us3cli du us3://<桶名字> [--projectid <项目ID>]
```

### 参数说明

```
      --accesskey <string>   :用于访问us3的API公钥或Token公钥
      --config <string>      :当前命令临时指定配置名/配置文件路径
      --endpoint <string>    :固定域名，可通过地域和域名页查看
  -h, --help                 :当前命令使用说明
      --projectid <string>   :指定当前bucket所在projectid，可以在控制台查看，默认项目可忽略
```

### 使用示例

- 查看bucket1所占空间大小，内容包含各存储类型的存储量以及总存储量

```
StorageClass    Size   
STANDARD:       1.05 GB
IA:             0 GB   
ARCHIVE:        0 GB   
Total storage:  1.05 GB
```

## cp

该命令用于上传、下载、拷贝文件

### 命令格式

上传文件

```
us3cli cp <本地文件路径> us3://<桶名字>/<文件Key> [--recursive][--hit modtime|etag][--parallel <分片上传并发数>][--speedlimit <速度限制>][--storageclass <存储类型>][--exclude <通配符表达式>][--rexclude <正则表达式>][--include <通配符表达式>][--rinclude <正则表达式>][--metadata <Key>=<value1>[,<key2>=<value2>]...][--mimetype <多媒体文件格式>]
```

下载文件

```
us3cli cp us3://<桶名字>/<文件Key> <本地文件路径> [--recursive] [--speedlimit <速度限制>][--exclude <通配符表达式>][--rexclude <正则表达式>][--include <通配符表达式>][--rinclude <正则表达式>][--parallel <分片下载并发数>][--partsize <下载分片range大小>]
```

拷贝文件

```
us3cli cp us3://<桶名字>/<文件Key> us3://<桶名字>/<文件Key> [--recursive][--exclude <通配符表达式>][--rexclude <正则表达式>][--include <通配符表达式>][--rinclude <正则表达式>][--metadata <Key>=<value1>[,<key2>=<value2>]...]
```

### 参数说明

```
      --accesskey <string>     :用于访问us3的API公钥或Token公钥
      --config <string>        :当前命令临时指定配置名/配置文件路径
      --endpoint <string>      :固定域名，可通过地域和域名页查看
      --exclude <string>       :不包含当前通配符的文件名
  -h, --help                   :当前命令使用说明
      --hit <string>           :可选项: modtime、etag
      		modtime            :在判断是否上传时采用文件最后修改时间作为判断标准，如果本地文件最后修改时间晚于us3，则进行上传请求，否则不上传
    	    etag:在判断是否上传时采用文件etag作为判断标准，如果本地文件etag和us3中的etag不同，则进行上传请求，否则不上传
      --include <string>       :包含当前通配符的文件名
      --metadata <string>      :指定元数据信息(该参数仅限上传和拷贝) 多个元数据以","分隔，如 "key1=value,key2=value2",其他分隔符暂不支持
      --mimetype <string>      :指定mimetype(该参数仅限上传)
      --parallel <int>         :分片并发数或批量并发数
      --partsize <string>      :下载range大小，默认大小为4M，最小值为4M(该参数仅限下载)
  -r, --recursive              :递归文件夹中的所有文件及子目录下所有文件
      --rexclude <string>      :不包含当前正则表达式的文件名
      --rinclude <string>      :包含当前正则表达式的文件名
      --secretkey <string>     :用于访问us3的API私钥或Token私钥
  -s, --speedlimit <string>    :平均速度限制(单位可以是B,KB,MB，不带单位默认以B/s计算)，默认200MB/s
      --storageclass <string>  :指定存储类型,对应有效值：STANDARD, IA, ARCHIVE(该参数仅限上传)，默认值：STANDARD
```

注意：

1.通配符表达式暂时只支持“*”,"?"两种字符，并且需要注意的是，四种表达式筛选均以当前目录下文件路径为准

 如：   us3://us3cli/test 目录下的test2/test3.txt  会以test2/test3.txt作为字符串筛选，而不是以test3.txt作为字符串进行筛选

2.以下所有speedlimit选项均描述为平均速度

3.上传时根据文件大小使用不同的请求，文件大于64MB使用分片上传，小于64MB时使用普通的put请求，故上传中的parallel参数仅限上传64MB以上大文件使用

### 使用示例

- 上传单个文件

```
./us3cli cp ~/go/src/test.txt  us3://bucket1/test
```

- 上传单个大文件(大于64MB)并设置分片并发数为10

```
./us3cli cp ~/go/src/test.mp4  us3://bucket1/test.mp4 --parallel 10
# 文件大小小于64MB时该参数无效
```

- 下载单个文件

```
./us3cli cp us3://bucket1/test/test.txt  ~/go/src/test.txt
```

- 拷贝单个文件

```
./us3cli cp us3://bucket1/test.txt  us3://bucket2/test.txt
```

- 拷贝文件夹

```
./us3cli cp -r us3://bucket1/test	us3://bucket2/test
```

- 下载文件夹

```
./us3cli cp  -r us3://bucket/test   ~/go/src/test
```

- 秒传文件

```
./us3cli cp  ~/go/src/test  us3://bucket/test --hit etag
```

- 指定存储类型上传

上传单个文件并指定存储类型为IA（低频访问）类型

```
./us3cli cp ~/go/src/test.txt  us3://bucket/path/test.txt --storageclass IA
```

- 限速上传

上传文件test.txt，并设置速度为1024Kb/s

```
./us3cli cp ~/go/src/test.txt us3://bucket/test.txt --speedlimit 1024Kb
```

- 批量上传

上传所有格式为jpg的文件（通配符）

```
./us3cli cp -r ~/go/src/test us3://bucket/test --include "*.jpg"
```

上传所有a开头b结尾的文件(正则表达式)

```
./us3cli cp -r ~/go/src/test us3://bucket/test --rinclude "a*b"
```

上传所有不包括a开头b结尾的文件(正则表达式)

```
./us3cli cp -r ~/go/src/test us3://bucket/test --rexclude "a*b"
```

上传所有文件名不包括001的文件（通配符）

```
./us3cli cp -r ~/go/src/test us3://bucket/test --exclude "*001*"
```

- 上传单个文件并指定元数据信息

```
./us3cli cp ~/go/src/test us3://bucket/test --metadata key1=value1,key2=value2
```

- 指定mimetype为text/plain上传

```
./us3cli cp ~/go/src/test us3://bucket/test --mimetype text/plain
```

## sync

本命令用于文件或目录的增量上传

### 命令格式

```
us3cli sync <本地文件路径> us3://<桶名字>/<文件Key> [--mode cache|remote][--ruler modtime|etag][--speedlimit <速度限制>][--retrycount <重试次数>][--exclude <通配符表达式>][--rexclude <正则表达式>][--include <通配符表达式>][--rinclude <正则表达式>][--parallel <请求并发数>]
```

### 参数说明

```
 	  --accesskey <string>     :用于访问us3的API公钥或Token公钥
      --config <string>        :当前命令临时指定配置名/配置文件路径
      --endpoint <string>      :固定域名，可通过地域和域名页查看
      --exclude <string>       :不包含当前通配符的文件名
  -h, --help                   :当前命令使用说明
      --include <string>       :包含当前通配符的文件名
  -m, --mode <string>          :可选项 cache、remote  默认值为cache
  			cache			   :以本地缓存为标准，检查基于缓存的增量文件，同步us3端对应目录的文件，默认为该模式
		    remote             :以本地文件系统为标准，检查本地文件以及us3不同步的文件，补全或删除us3端对应目录的文件	
      --parallel <int>         :请求并发数，默认值为10
      --retrycount: <int>      :失败重试次数,默认值：10
      --rexclude <string>      :不包含当前正则表达式的文件名
      --rinclude <string>      :包含当前正则表达式的文件名
      --ruler <string>         :可选项 modtime、etag  默认为modtime
  			modtime			   :在判断是否上传时采用文件最后修改时间作为判断标准，如果本地文件最后修改时间晚于us3，则进行上传请求，否则不上传
		    etag               :在判断是否上传时采用文件etag作为判断标准，如果本地文件etag和us3中的etag不同，则进行上传请求，否则不上传
	  --secretkey <string>     :用于访问us3的API私钥或Token私钥  
  -s, --speedlimit <string>    :平均速度限制(单位可以是B,KB,MB，不带单位默认以B/s计算)，默认200MB/s
```

增量模式说明：

1.cache模式使用本地缓存，从本地上传到bucket成功的文件，都会被记录为上传成功文件，如果需要重新上传，可以选择删除当前用户目录下的.us3cliconfig/leveldb文件夹，使用命令时会自动创建新的文件夹。

2.remote模式下最终以本地文件为标准，保证bucket中的目标文件夹和本地同步，`以下场景会进行文件删除，请慎用`：

增量上传文件夹后，将本地文件删除，再次使用remote模式增量，会将bucket中的文件删除以保持US3 Bucket和本地同步

### 使用示例

- 增量上传，默认只检查us3cli缓存相对us3增量的数据，忽略本地已上传但us3再次删除的文件

```
./us3cli sync /root/test us3://bucket/path
```

- 全量同步，检查所有不一致的文件并us3端文件与本地同步

```
./us3cli sync --mode remote /root/test us3://bucket/path 
```

- 限速为1024 Kb/s上传

```
./us3cli sync /root/test us3://bucket/path --speedlimit 1024Kb
```

## rcat

本命令用于流式上传文件

### 命令格式

```
us3cli rcat us3://<桶名字>/<文件Key>	[-retrycount <重试次数>][--speedlimit <速度限制>][--parallel <并发数限制>]
```

### 参数说明

```
	  --accesskey <string>     :用于访问us3的API公钥或Token公钥
      --config <string>        :当前命令临时指定配置名/配置文件路径
      --endpoint <string>      :固定域名，可通过地域和域名页查看
  -h, --help                   :当前命令使用说明
      --parallel <int>	       :分片并发数
      --retrycount: <int>      :失败重试次数,默认值：10
	  --secretkey <string>     :用于访问us3的API私钥或Token私钥
  -s, --speedlimit <string>    :平均速度限制(单位可以是B,KB,MB，不带单位默认以B/s计算)，默认200MB/s
```

### 使用示例

- 上传流式数据到us3中

```
cat test.txt | ./us3cli rcat us3://bucket1/test.txt
```

- 流式上传文件test.txt并设置限速为 2MB/s

```
cat test.txt | ./us3cli rcat us3://bucket1/test.txt --speedlimit 2MB
```

- 流式上传文件test.txt并设置重试次数为5次

```
cat test.txt | ./us3cli rcat us3://bucket1/test.txt --retrycount 5
```

- 流式上传文件test.txt并设置并发数为2

```
cat test.txt | ./us3cli rcat us3://bucket1/test.txt --parallel 2
```

## mkdir

本命令用于在US3存储桶内创建目录

### 命令格式

```
us3cli mkdir [--parents] us3://<桶名字>/<目录名>[/<目录名>]
```

### 参数说明

```
      --accesskey <string>   :用于访问us3的API公钥或Token公钥
      --config <string>      :当前命令临时指定配置名/配置文件路径
      --endpoint <string>    :固定域名，可通过地域和域名页查看
  -h, --help                 :当前命令使用说明
  -p, --parents              :用于创建多级目录
      --secretkey <string>   :用于访问us3的API私钥或Token私钥
```

### 使用示例

- 创建目录

```
./us3cli mkdir us3://bucket/dir
```

- 创建多级目录

````
./us3cli mkdir -p us3://bucket/dir1/dir2
````


## cat

本命令用于流式下载文件，

### 命令格式

```
us3cli cat us3://<bucketname>/<keyname> [--retrycount <重试次数>][--speedlimit <速度限制>]
```

### 参数说明

```
      --accesskey <string>     :用于访问us3的API公钥或Token公钥
      --config <string>        :当前命令临时指定配置名/配置文件路径
      --endpoint <string>      :固定域名，可通过地域和域名页查看
  -h, --help                   :当前命令使用说明
      --retrycount: <int>      :失败重试次数,默认值：10
	  --secretkey <string>     :用于访问us3的API私钥或Token私钥
  -s, --speedlimit <string>    :平均速度限制(单位可以是B,KB,MB，不带单位默认以B/s计算)，默认200MB/s
```

注意：本工具所有的数据传输默认情况下均为不限速，单位可以是B/KB/MB或直接输入数字，数字以B/S 计算，这里所有的B均指字节。

### 使用示例

- 查看test.txt文件内容

```
./us3cli cat us3://bucket1/test.txt
```

- 流式下载test.txt到本地

```
./us3cli cat us3://bucket1/test.txt > test.txt
```

- 流式下载并限速为1MB/s

```
./us3cli cat us3://bucket1/test.txt --speedlimit 1MB > test.txt
```

- 流式下载并设置重试次数为5次

```
./us3cli cat us3://bucket1/test.txt --retrycount 5 > test.txt
```

## mv

本命令用于移动文件

注意：本命令只能用于相同bucket之内，同时-f选项只支持文件

### 命令格式

```
us3cli mv us3://<桶名字>/<文件Key> us3://<桶名字>/<文件Key> [--force][--exclude <通配符表达式>][--rexclude <正则表达式>][--include <通配符表达式>][--rinclude <正则表达式>][--parallel <并发数>]
```

### 参数说明

```
	  --accesskey <string>     :用于访问us3的API公钥或Token公钥
      --config <string>        :当前命令临时指定配置名/配置文件路径
      --endpoint <string>      :固定域名，可通过地域和域名页查看
      --exclude <string>       :不包含当前通配符的文件名
  -f, --force                  :存在同名文件时是否覆盖
  -h, --help                   :当前命令使用说明
      --include <string>       :包含当前通配符的文件名
      --parallel <int>         :并发数
      --rexclude <string>      :不包含当前正则表达式的文件名
      --rinclude <string>      :包含当前正则表达式的文件名
      --secretkey <string>     :用于访问us3的API私钥或Token私钥
```

### 使用示例

- 移动文件

```
./us3cli mv us3://bucket/test.txt   us3://bucket/test/test.txt				
```

- 强制移动文件

```
./us3cli mv us3://bucket/test.txt   us3://bucket/test/test.txt -f
```

- 移动文件夹
- 注意：目标文件夹存在时，文件夹会被移动到目标文件夹子目录中

``` 
./us3cli mv us3://bucket/test  us3://bucket/test2  				
```

## rm

本命令用于删除目录或者对象

### 命令格式

```
us3cli rm us3://<桶名字>/<文件Key> [--recursive][--force][--exclude <通配符表达式>][--rexclude <正则表达式>][--include <通配符表达式>][--rinclude <正则表达式>][--qps <每秒最大请求数量>]
```

### 参数说明

```
 	  --accesskey <string>     :用于访问us3的API公钥或Token公钥
      --config <string>        :当前命令临时指定配置名/配置文件路径
      --endpoint <string>      :固定域名，可通过地域和域名页查看
      --exclude <string>       :不包含当前通配符的文件名
  -f, --force                  :强制删除
  -h, --help                   :当前命令使用说明
      --include <string>       :包含当前通配符的文件名
      --parallel <int>         :批量删除并发数，默认值为10
  -q, --qps <int>              :限制每秒请求数量，默认50qps，取值范围：1~1000
  -r, --recursive              :递归文件夹中的所有文件及子目录下所有文件
      --rexclude <string>      :不包含当前正则表达式的文件名
      --rinclude <string>      :包含当前正则表达式的文件名
	  --secretkey <string>     :用于访问us3的API私钥或Token私钥  
```

### 使用示例

- 删除文件

```
./us3cli rm us3://bucket/test.txt
```

- 强制删除文件

```
./us3cli rm us3://bucket/test.txt
```

- 强制删除文件夹

```
./us3cli rm -r -f us3://bucket/test
```

- 批量删除test文件夹中后缀名为.jpg的文件

```
./us3cli rm -r us3://bucket/test --include  "*.jpg" 
```

- 限制删除操作最多每秒10个请求，最大可以上调到1000qps，取值范围在1~1000

```
./us3cli rm -r us3://bucket/test --include  "*.jpg" --qps 10
```

- 批量删除文件并设置并发数为30

```
./us3cli rm -r us3://bucket/test --parallel 30
```

- 清空bucket

```
./us3cli rm -r -f us3://bucket --parallel 10
```

## ls

该命令用于列出存储桶(Bucket)、对象（Object）

### 命令格式

```
us3cli ls [us3://<桶名称>] [--limit <输出限制数>][--restore][--flat][--projectid <项目ID>]
```

### 参数说明

```
	  --accesskey <string>   :用于访问us3的API公钥或Token公钥
      --config <string>      :当前命令临时指定配置名/配置文件路径
      --endpoint <string>    :固定域名，可通过地域和域名页查看
      --flat                 :是否以非层级结构（展开所有目录）显示,默认层级显示
  -h, --help                 :当前命令使用说明
  -l, --limit <int>          :需要同时列出的最大文件条数，默认值为100
      --projectid <string>   :列出bucket时指定projectid，可以在控制台查看，默认项目可忽略
  -r, --restore              :是否展示数据解冻信息
      --secretkey <string>   :用于访问us3的API私钥或Token私钥
```

### 使用示例

- 列出当前权限下所有bucket

```
us3cli ls

BucketName      Region  Acl     CreateTime         
bucket1     	cn-bj   public  2020-03-27 17:20:56
bucket2      	cn-bj   private 2020-03-27 17:20:43
us3cli          cn-bj   public  2020-09-15 16:17:24
```

- 列出bucket下所有对象

```
./us3cli ls us3://bucket

Key                     FileSize   StorageClass         Etag                             CreateTime      
us3://bucket/cf1   		DIR        IA                   AQAAANo5o-5ea0sNMlW_75VgGJCv2AcJ 2020-07-16 18:51:04  
us3://bucket/aa.txt     4KB        STANDARD             AQAAAGNlfLt9cIbFawzU5caZm7aDZkho 2020-07-22 11:04:34 
us3://bucket/aa.txt     4KB        ARCHIVE              AQAAAGNlfLt9cIbFawzU5caZm7aDZkho 2020-07-22 11:04:34 
```

- 列出bucket下对象并限制显示文件数量为2

```
./us3cli ls us3://bucket --limit 2
```

- 列出bucket下对象并显示数据解冻状态信息

```
./us3cli ls us3://bucket --restore
```

- 递归当前目录列出非目录形式的文件列表（展开所有目录）

```
./us3cli ls us3://bucket --flat
```

## sign

本命令用于文件下载url获取

### 命令格式

```
us3cli sign us3://<桶名字>/<文件Key>   [--expires <时间>]
```

### 参数说明

```
 	  --accesskey <string>     :用于访问us3的API公钥或Token公钥
      --config <string>        :当前命令临时指定配置名/配置文件路径
      --endpoint <string>      :固定域名，可通过地域和域名页查看
      --expires <int>          :url使用的过期时间,单位s
  -h, --help                   :当前命令使用说明
	  --secretkey <string>     :用于访问us3的API私钥或Token私钥  
```

### 使用示例

- 获取url签名

```
./us3cli sign us3://bucket/test.txt
```

- 获取限时100s 的url签名

```
./us3cli sign us3://bucket/test.txt --expires 100
```

## modify

该命令用于修改us3中的文件信息

### 命令格式

```
us3cli modify us3://<桶名字>/<文件Key> [--storageclass <存储类型>][--mimetype <多媒体文件格式>][--metadata <<key1=value1>[,<key2=value2>]...>][--replace]
```

### 参数说明

```
	  --accesskey <string>     :用于访问us3的API公钥或Token公钥
      --config <string>        :当前命令临时指定配置名/配置文件路径
      --endpoint <string>      :固定域名，可通过地域和域名页查看
  -h, --help                   :当前命令使用说明
      --metadata <string>      :指定元数据信息 
      --mimetype <string>      :指定多媒体文件格式mimetype
      --replace                :是否清空原有元数据
      --secretkey <string>     :用于访问us3的API私钥或Token私钥
      --storageclass <string>  : 指定存储类型,对应有效值：STANDARD, IA, ARCHIVE
```

### 使用示例

- 存储类型改变

修改test.txt的存储类型为STANDARD标准型

```
./us3cli modify us3://bucket/test.txt --storageclass STANDARD
```

- mimetype改变

修改test.txt文件的mimetype信息

```
./us3cli modify us3://bucket/test.txt --mimetype image/x-icon
```

- metadata改变

1.修改test.txt文件的元数据信息，以key=value形式作为参数，可修改多个元数据信息，中间以英文逗号","分隔.

```
./us3cli modify us3://bucket/test.txt --metadata "key1=value1,key2=value2"
```

2.清除原有元数据，添加新的内容

```
./us3cli modify us3://bucket/test.txt --metadata "key1=value1" --replace
```

3.删除所有元数据

```
./us3cli modify us3://bucket/test.txt --metadata "" --replace
```

## restore

本命令用于恢复冷冻状态的对象（object）为可读状态

### 命令格式

```
us3cli restore us3://<桶名字>/<文件Key> [--recursive][--qps <每秒请求数上限>][--exclude <通配符表达式>][--rexclude <正则表达式>][--include <通配符表达式>][--rinclude <正则表达式>][--parallel <批量激活并发数>]
```

### 参数说明

```
 	  --accesskey <string>     :用于访问us3的API公钥或Token公钥
      --config <string>        :当前命令临时指定配置名/配置文件路径
      --endpoint <string>      :固定域名，可通过地域和域名页查看
      --exclude <string>       :不包含当前通配符的文件名
  -h, --help                   :当前命令使用说明
      --include <string>       :包含当前通配符的文件名
      --parallel <int>         :批量激活并发数，默认值为10
  -q, --qps <int>              :限制每秒请求数量，默认50qps，取值范围：1~1000
  -r, --recursive              :递归文件夹中的所有文件及子目录下所有文件
      --rexclude <string>      :不包含当前正则表达式的文件名
      --rinclude <string>      :包含当前正则表达式的文件名
	  --secretkey <string>     :用于访问us3的API私钥或Token私钥  
```

### 使用示例

- 单个文件数据解冻

```
./us3cli restore us3://bucket/test.txt
```

- 数据解冻整个目录中所有文件

```
./us3cli restore -r us3://bucket/test
```

- 批量激活文件并设置并发数为30

```
./us3cli restore -r us3://bucket/test --parallel 30
```

## etag

该命令用于计算文件etag值

### 命令格式

```
us3cli etag [<本地文件路径1>,[<本地文件路径2>] ...] [us3://<桶名字>/<文件Key>] [-]
```

### 参数说明

```
 固定参数:  "-" 表示计算标准输入中的数据流etag值
      --accesskey <string>   :用于访问us3的API公钥或Token公钥
      --config <string>      :当前命令临时指定配置名/配置文件路径
      --endpoint <string>    :固定域名，可通过地域和域名页查看
  -h, --help                 :当前命令使用说明
      --secretkey <string>   :用于访问us3的API私钥或Token私钥
```

### 使用示例

- 计算本地文件etag值

```
./us3cli etag main.go

Name              Etag                            
main.go           AQAAAEpmwm87EANJQDpLTEmxsjR7-R0N
```

- 计算us3文件etag值

```
./us3cli etag us3://bucket1/test.txt

Name                             Etag                            
us3://bucket1/test.txt           AQAAAEpmwm87EANJQDpLTEmxsjR7-R0N
```

- 计算标准输入etag值

```
cat test.txt | ./us3cli etag -

Name    Etag                        
-       SmbCbzsQA0lAOktMSbGyNHv5HQ0=
```

- 计算多个文件etag

```
cat test4.txt | ./us3cli etag us3://bucket1/test.txt us3://bucket1/test2.txt test3.txt -

Name                                    Etag                            
us3://bucket1/test.txt                  AQAAAEpmwm87EANJQDpLTEmxsjR7-R0N
us3://bucket1/test2.txt                 AQAAAEpmwm87EANJQDpLTEmxsjR7-R0N
test3.txt    							AQAAAHPuBl-6VRpzVHiBFjSOVhLrcsam
-                                       SmbCbzsQA0lAOktMSbGyNHv5HQ0= 
```

## update

本命令用于版本更新

注意：

1.windows用户更新后会产生一个名为config.temp的临时文件，在下一次运行us3cli时会自动删除。

2.将本工具放入bin目录下执行时，更新操作会出现找不到文件的问题，具体解决方案请查看常见问题页面。

### 命令格式

```
us3cli update [--force]
```

### 参数说明

```
  -f, --force   :忽略版本，强制更新
  -h, --help    :当前命令使用说明
```

##### 使用示例

```
./us3cli update

Your version is not the latest version, do you update it now(y or n):y
Downloaded latest version package,saved as: us3cli-linux64
```

## version

本命令用于查看工具版本

### 命令格式

```
us3cli version
```

### 使用示例

```
./us3cli version

us3cli version 1.0.5
```
