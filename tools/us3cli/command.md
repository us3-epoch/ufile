# 常用命令



| 操作           | 命令                | 描述                                                         |
| -------------- | ------------------- | ------------------------------------------------------------ |
| 配置管理       | [config](#config)   | 管理上传所需的公私钥及endpoint等信息，包括配置项的创建、修改、删除、更新、切换 |
| 创建存储空间     | [mb](#mb)           | 创建存储空间                                                   |
| 删除存储空间     | [rb](#rb)           | 删除存储空间（存储空间为空）                                     |
| 查看存储空间信息 | [stat](#stat)       | 查看bucket的元数据信息                                       |
| 查看存储量     | [du](#du)           | 查看bucket的存储量（标准、低频、归档）                       |
| 普通上传       | [cp](#cp)           | 将本地文件或目录上传到存储空间中                               |
| 增量上传       | [sync](#sync)       | 增量上传目录到存储空间中                                       |
| 流式上传       | [rcat](#rcat)       | 将流式文件上传到存储空间中                                     |
| 创建目录       | [mkdir](#mkdir)     | 在us3存储空间中创建一个空目录                                |
| 普通下载       | [cp](#cp)           | 将存储空间中的文件或目录下载到本地                             |
| 流式下载       | [cat](#cat)         | 将存储空间中的数据下载并写入到标准输入中                       |
| 拷贝           | [cp](#cp)           | 将一个存储空间中的文件拷贝到另一个存储空间中（同一地域）         |
| 移动           | [mv](#mv)           | 将文件或目录移动到其他目录下（同一存储同内）                 |
| 删除           | [rm](#rm)           | 删除存储空间中的文件或目录                                     |
| 列表           | [ls](#ls)           | 列出US3存储空间列表或US3存储空间中的文件列表                     |
| 获取下载URL    | [sign](#sign)       | 获取存储空间中文件的下载链接                                   |
| 查看元数据     | [stat](#stat)       | 查看存储空间中文件的元数据信息                                 |
| 修改元数据     | [modify](#modify)   | 修改存储空间中文件的存储类型、MIMETYPE、MetaData               |
| 归档数据取回   | [restore](#restore) | 将归档类型的文件激活为可下载状态                             |
| 数据完整性校验 | [etag](#etag)       | 查看本地文件、标准输出、US3存储空间的文件etag                  |
| 版本更新       | [update](#update)   | 更新工具版本                                                 |
| 版本特性       | [version](#version) | 查看工具版本特性                                             |

## config

config命令用于管理配置文件。

### 命令格式

```
us3cli config  [--ls][--su <配置名>][--rm <配置名>][--cat <配置名>][--encrypt][--ssl][--proxy [代理地址]]
			   [--accesskey <API/Token公钥>][--secretkey <API/Token私钥>][--endpoint <访问域名>]
```

### 参数说明

```
  -a, --accesskey string   :用于访问us3的API密钥或Token公钥
      --cat string         :打印指定配置项内容
      --encrypt            :是否配置加密
  -e, --endpoint string    :固定域名，可在地域和域名页查看
  -h, --help               :查看当前命令帮助
      --ls                 :列出当前所有配置项
      --proxy string       :代理地址(ip:port)
      --rm string          :删除指定配置项
  -s, --secretkey string   :用于访问us3的API私钥或Token私钥
      --ssl                :使用https
      --su string          :切换指定配置为默认配置
```

配置文件内容说明：

| 配置项    | 说明                 | 填写说明                                                     |
| --------- | -------------------- | ------------------------------------------------------------ |
| AccessKey | 用于鉴权的bucket公钥 | [API公钥](https://console.ucloud.cn/uapi/apikey) 、[Token公钥](https://console.ucloud.cn/ufile/token) |
| SecretKey | 用于鉴权的bucket私钥 | [API私钥](https://console.ucloud.cn/uapi/apikey) 、[Token私钥](https://console.ucloud.cn/ufile/token) |
| Endpoint  | 外网或内网域名       | [地域和域名](https://docs.ucloud.cn/ufile/introduction/region) |
| encrypt   | 是否使用配置加密     | false 或 true                                                |
| enablessl | 是否使用https        | false 或 true                                                |
| proxy     | 代理地址             | “ip:port”                                                    |

自定义配置文件格式，填写说明同上：

```
accesskey: "user accesskey"
secretkey: "user secretkey"
endpoint: "ufile.cn-north-02.ucloud.cn"
encrypt: "false"
enablessl: "false"
proxy: "http://ip:port or https://ip:port"
```

### 使用示例

1.交互式配置

- 创建配置项

```
#./us3cli config
请输入当前配置项名称: config1
开始创建新配置项：[ config1 ]
是否启用配置加密(y or n) ? n
请输入API/Token公钥[当前:]: xxxxxxxxxxxxxxxxxxxxxx
请输入API/Token私钥[当前:]: xxxxxxxxxxxxxxxxxxxxxx
地区列表：
No.     RegionName      Region      
0       北京		cn-bj       
1       上海二		cn-sh2      
2       广州		cn-gd       
3       香港		hk          
4       洛杉矶		us-ca       
5       新加坡		sg          
6       雅加达		idn-jakarta 
7       台北		tw-tp       
8       拉各斯		afr-nigeria 
9       圣保罗		bra-saopaulo
10      迪拜		uae-dubai   
11      法兰克福		ge-fra      
12      胡志明市		vn-sng      
13      华盛顿		us-ws       
14      孟买		ind-mumbai  
15      首尔		kr-seoul    
请输入region编号: 0
内外网列表：
No.     Network
0       外网   
1       内网   
请选择或输入内外网编号：0
您选择的endpoint是：[cn-bj.ufileos.com],[当前:]，请输入回车确认或自定义endponit：
当前最终配置：
ConfigName: config1
AccessKey: xxxxxxxxxxxxxxxxxxxxxx
SecretKey: xxxxxxxxxxxxxxxxxxxxxx
Endpoint: cn-bj.ufileos.com
请检查后输入回车确认：
是否启用HTTPS (y or n) ? n
是否启用代理(y or n):n
配置文件 [ config1 ] 已修改
是否使用该配置作为默认配置(当前默认配置为：< config >)(y or n)?
```

注意：

1. 首次创建的配置文件时会自动将该配置作为默认配置
2. 配置加密只加密到公私钥，并且当前配置文件只在初次创建时可选是否加密
3. 填写代理地址时只需要填写“ ip:port”,客户端会根据填写的https启用状态补充需要的"https://"或“http://”头部信息

- 列出配置项列表

```
./us3cli config --ls

ConfigName              ModTime                 FilePath                         Authority                 
config1  (Default)      2020-09-21 14:18:50     /root/.us3cliconfig/config1      Token       
config2                 2020-09-21 14:18:50     /root/.us3cliconfig/config2      Token     
us3cli                  2020-09-16 10:36:00     /root/.us3cliconfig/us3cli       APIKey
```

说明：

1.Default标识表示该配置项是当前的默认配置

2.Authority表示权限分类，只用于快速区分Token和API密钥格式，不保证内容准确

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
ConfigName：config2
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

该命令用于创建存储空间

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
      --projectid <string>   :项目ID，当前bucket属项目ID，默认为Default
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
0       北京		cn-bj       
1       上海二		cn-sh2      
2       广州		cn-gd       
3       香港		hk          
4       洛杉矶		us-ca       
5       新加坡		sg          
6       雅加达		idn-jakarta 
7       台北		tw-tp       
8       拉各斯		afr-nigeria 
9       圣保罗		bra-saopaulo
10      迪拜		uae-dubai   
11      法兰克福		ge-fra      
12      胡志明市		vn-sng      
13      华盛顿		us-ws       
14      孟买		ind-mumbai  
15      首尔		kr-seoul    
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

注意：若您的bucket不在默认项目下，bucket操作都需要加上--projectid参数，请创建bucket时自行记录projectid，或通过 [ls](#ls) 命令查看projectid


## rb

本命令用于删除存储空间

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
      --projectid <string>     :指定当前bucket所在projectid，可以在控制台查看，默认项目可忽略
      --secretkey <string>     :用于访问us3的API私钥或Token私钥
```

### 使用示例

- 删除存储空间:存储空间必须为空，否则无法删除

```
./us3cli rb us3://bucket1
The bucket [bucket1] is being deleted, continue(y or n)? y
Delete bucket [bucket1] success
```

## stat

本命令用于查看存储空间或文件信息

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

- 查看默认项目下存储空间bucket1的基本信息

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

该命令用于获取指定存储空间(Bucket)各存储类型的存储量大小，以及总存储量

注意：存储量结算时间为当前日期前一天0:00

### 命令格式

```
us3cli du us3://<桶名字>[/<key>] [--force][--projectid <项目ID>]
```

### 参数说明

```
      --accesskey <string>   :用于访问us3的API公钥或Token公钥
      --config <string>      :当前命令临时指定配置名/配置文件路径
      --endpoint <string>    :固定域名，可通过地域和域名页查看
  -f, --force                :强制更新存储量
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

- 查看bucket1下的testfolder目录下各类型存储量以及文件数量

```
#./us3cli du us3://bucket1/testfolder

			    FileCount       FileSize
Sum             10000           99.77 MB 
STANDARD        10000           99.77 MB 
IA              0               0 B     
ARCHIVE         0               0 B     
UpdateTime: 2020-12-24 11:40:27 Thu
```

注意:当使用命令首次查看某个目录或前缀下的存储量时，会将数据保存在本地，24内使用非强制更新查看存储量时，都不会重新请求，只显示前24小时内拉取到的最新数据。有更新需要的可以添加-f/--force选项再次拉取存储量。

## cp

该命令用于上传、下载、拷贝文件

### 命令格式

上传文件

```
us3cli cp <本地文件路径> us3://<桶名字>/<文件Key> [--check][--recursive][--reduce][--parallel <分片上传并发数>][--speedlimit <速度限制>][--storageclass <存储类型>][--exclude <通配符表达式>][--rexclude <正则表达式>][--include <通配符表达式>][--rinclude <正则表达式>][--metadata <Key>=<value1>[,<key2>=<value2>]...][--mimetype <多媒体文件格式>]
```

下载文件

```
us3cli cp us3://<桶名字>/<文件Key> <本地文件路径> [--recursive][--reduce][--speedlimit <速度限制>][--exclude <通配符表达式>][--rexclude <正则表达式>][--include <通配符表达式>][--rinclude <正则表达式>][--parallel <分片下载并发数>][--partsize <下载分片大小>]
```

拷贝文件

```
us3cli cp us3://<桶名字>/<文件Key> us3://<桶名字>/<文件Key> [--recursive][--reduce][--exclude <通配符表达式>][--rexclude <正则表达式>][--include <通配符表达式>][--rinclude <正则表达式>][--metadata <Key>=<value1>[,<key2>=<value2>]...]
```

### 参数说明

```
      --accesskey <string>     :用于访问us3的API公钥或Token公钥
  -c, --check                  :用于上传文件夹结束后检查已上传文件的etag是否一致
      --config <string>        :当前命令临时指定配置名/配置文件路径
      --endpoint <string>      :固定域名，可通过地域和域名页查看
      --exclude <string>       :不包含当前通配符的文件名
  -h, --help                   :当前命令使用说明
      --include <string>       :包含当前通配符的文件名
      --metadata <string>      :指定元数据信息(该参数仅限上传和拷贝) 多个元数据以","分隔，如 "key1=value,key2=value2",其他分隔符暂不支持
      --mimetype <string>      :指定mimetype(该参数仅限上传)
      --parallel <int>         :分片并发数或批量并发数
      --partsize <string>      :下载分片大小，默认大小为4M，最小值为1M(该参数仅限下载)
  -r, --recursive              :递归文件夹中的所有文件及子目录下所有文件
      --reduce                 :是否以精简模式执行（不展示进度）
      --rexclude <string>      :不包含当前正则表达式的文件名
      --rinclude <string>      :包含当前正则表达式的文件名
      --secretkey <string>     :用于访问us3的API私钥或Token私钥
  -s, --speedlimit <string>    :平均速度限制(单位可以是B,KB,MB，不带单位默认以B/s计算)，默认200MB/s
      --storageclass <string>  :指定存储类型,对应有效值：STANDARD, IA, ARCHIVE(该参数仅限上传)，默认值：STANDARD
```

注意：

1.通配符表达式暂时只支持“*”,"?"两种字符，并且需要注意的是，四种表达式筛选均以当前目录下文件路径为准

如：us3://us3cli/test 目录下的test2/test3.txt  会以test2/test3.txt作为字符串筛选，而不是以test3.txt作为字符串进行筛选

2.以下所有speedlimit选项均描述为平均速度

3.单个文件上传时根据文件大小使用不同的请求，文件大于64MB使用分片上传，小于64MB时使用普通的put请求，故上传中的parallel参数仅限上传64MB以上大文件使用

4.由于windows环境下文件名存在不区分大小写的情况，`要下载的文件已存在时，会在文件名及文件后缀名之间默认添加(1),(2)...等作为文件名后缀`，如：

```
文件test.txt存在，则下载的文件保存为test(1).txt，若此时test(1).txt也存在，也保存为test(2).txt，依次类推
```

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

- 上传文件夹并检查etag是否一致

```
./us3cli cp -r ~/go/src/test us3://bucket/test --check 
```

## sync

本命令用于目录的增量上传

### 命令格式

```
us3cli sync <本地目录> us3://<桶名字>/<文件Key> [--reduce][--mode cache|local][--ruler modtime|etag][--speedlimit <速度限制>][--retrycount <重试次数>][--exclude <通配符表达式>][--rexclude <正则表达式>][--include <通配符表达式>][--rinclude <正则表达式>][--parallel <请求并发数>][--metadata <Key>=<value1>[,<key2>=<value2>]...][--mimetype <多媒体文件格式>][--storageclass <存储类型>]
```

### 参数说明

```
      --accesskey <string>     :用于访问us3的API公钥或Token公钥
      --config <string>        :当前命令临时指定配置名/配置文件路径
      --endpoint <string>      :固定域名，可通过地域和域名页查看
      --exclude <string>       :不包含当前通配符的文件名
  -h, --help                   :当前命令使用说明
      --include <string>       :包含当前通配符的文件名
      --metadata <string>      :指定元数据信息，多个元数据以","分隔，如 "key1=value,key2=value2",其他分隔符暂不支持
      --mimetype <string>      :指定mimetype上传
  -m, --mode <string>          :可选项 cache、local  默认值为cache
        cache                  :以本地缓存为标准，检查基于缓存的增量文件，同步us3端对应目录的文件，默认为该模式
        local                  :以本地文件系统为标准，检查本地文件以及us3不同步的文件，补全或删除us3端对应目录的文件	
      --parallel <int>         :请求并发数，默认值为10
      --reduce                 :是否以精简模式执行（不展示进度）
  -r, --retrycount: <int>      :失败重试次数,默认值：10
      --rexclude <string>      :不包含当前正则表达式的文件名
      --rinclude <string>      :包含当前正则表达式的文件名
      --ruler <string>         :可选项 modtime、etag  默认为modtime
        modtime                :在判断是否上传时采用文件最后修改时间作为判断标准，如果本地文件最后修改时间晚于us3，则进行上传请求，否则不上传
        etag                   :在判断是否上传时采用文件etag作为判断标准，如果本地文件etag和us3中的etag不同，则进行上传请求，否则不上传
      --secretkey <string>     :用于访问us3的API私钥或Token私钥  
  -s, --speedlimit <string>    :平均速度限制(单位可以是B,KB,MB，不带单位默认以B/s计算)，默认200MB/s
      --storageclass <string>  :指定存储类型,对应有效值：STANDARD, IA, ARCHIVE，默认值：STANDARD
```

增量模式说明：

1.cache模式使用本地缓存，从本地上传到bucket成功的文件，都会被记录为上传成功文件，如果需要重新上传，可以选择删除当前用户目录下的.us3cliconfig/leveldb文件夹，使用命令时会自动创建新的文件夹。

2.local模式下最终以本地文件为标准，保证bucket中的目标文件夹和本地同步，`以下场景会进行文件删除，请慎用`：

增量上传文件夹后，将本地文件删除，再次使用local模式增量，会将bucket中的文件删除以保持US3 Bucket和本地同步

### 使用示例

- cache模式，以本地缓存中保存的修改时间为标准，同步本地文件

```
./us3cli sync /root/test us3://bucket/path
```

- cache模式，以本地缓存中的文件etag信息为标准，同步本地文件

```
./us3cli sync /root/test us3://bucket/path --ruler etag
```

- local模式，对比本地文件夹与指定us3目录中的所有文件的修改时间，通过上传文件以及删除us3文件操作，将us3目录状态与本地保持一致(如us3中有相对于本地多余的文件，则会删除us3中对应多余文件，请慎用)

```
./us3cli sync /root/test us3://bucket/path --mode local 
```

- local模式，对比本地文件夹与指定us3目录中的所有文件的etag信息，通过上传文件以及删除us3文件操作，将us3目录状态与本地保持一致(如us3中有相对于本地多余的文件，则会删除us3中对应多余文件，请慎用)

```
./us3cli sync /root/test us3://bucket/path --mode local --ruler etag
```

- 限速为1024 Kb/s上传

```
./us3cli sync /root/test us3://bucket/path --speedlimit 1024Kb
```

- 并发上传文件夹

```
./us3cli sync /root/test us3://bucket/path --parallel 5
```

- 指定mimetype上传

```
./us3cli sync /root/test us3://bucket/path --mimetype "mimetype"
```

- 指定存储类型上传

```
./us3cli sync /root/test us3://bucket/path --storageclass "ARCHIVE"
```

- 指定元数据上传

```
./us3cli sync /root/test us3://bucket/path --metadata "key1=value1,key2=value2"
```

## rcat

本命令用于流式上传文件

### 命令格式

```
us3cli rcat us3://<桶名字>/<文件Key>	[--reduce][-retrycount <重试次数>][--speedlimit <速度限制>][--parallel <并发数限制>]
```

### 参数说明

```
      --accesskey <string>     :用于访问us3的API公钥或Token公钥
      --config <string>        :当前命令临时指定配置名/配置文件路径
      --endpoint <string>      :固定域名，可通过地域和域名页查看
  -h, --help                   :当前命令使用说明
      --parallel <int>	       :分片并发数
      --reduce                 :是否以精简模式执行（不展示进度）
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

本命令用于在US3存储空间内创建目录

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

本命令用于流式下载文件

### 命令格式

```
us3cli cat us3://<bucketname>/<keyname> [--reduce][--retrycount <重试次数>][--speedlimit <速度限制>][--partsize <下载分片大小>]
```

### 参数说明

```
      --accesskey <string>     :用于访问us3的API公钥或Token公钥
      --config <string>        :当前命令临时指定配置名/配置文件路径
      --endpoint <string>      :固定域名，可通过地域和域名页查看
  -h, --help                   :当前命令使用说明
      --partsize               :下载分片大小，默认大小为4M，最小值为1M(该参数仅限下载)
      --reduce                 :是否以精简模式执行（不展示进度）
      --retrycount: <int>      :失败重试次数,默认值：10
      --secretkey <string>     :用于访问us3的API私钥或Token私钥
  -s, --speedlimit <string>    :平均速度限制(单位可以是B,KB,MB，不带单位默认以B/s计算)，默认200MB/s
```

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

- 下载并设置分片大小为2M

```
./us3cli cat us3://bucket1/test.txt --partsize 2M > test.txt
```

## mv

本命令用于移动文件

注意：本命令只能用于相同bucket之内，同时-f选项只支持文件

### 命令格式

```
us3cli mv us3://<桶名字>/<文件Key> us3://<桶名字>/<文件Key> [--force][--reduce][--exclude <通配符表达式>][--rexclude <正则表达式>][--include <通配符表达式>][--rinclude <正则表达式>][--parallel <并发数>]
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
      --reduce 		       :是否以精简模式执行（不展示进度）
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
  注意：目标文件夹存在时，文件夹会被移动到目标文件夹子目录中

``` 
./us3cli mv us3://bucket/test  us3://bucket/test2  				
```

## rm

本命令用于删除目录或者对象

### 命令格式

```
us3cli rm us3://<桶名字>/<文件Key> [--recursive][--force][--reduce][--exclude <通配符表达式>][--rexclude <正则表达式>][--include <通配符表达式>][--rinclude <正则表达式>][--qps <每秒最大请求数量>]
```

### 参数说明

```
      --accesskey <string>     :用于访问us3的API公钥或Token公钥
      --cache  <string>        :要删除的缓存类型 : upload|download|sync|du
      --config <string>        :当前命令临时指定配置名/配置文件路径
      --endpoint <string>      :固定域名，可通过地域和域名页查看
      --exclude <string>       :不包含当前通配符的文件名
  -f, --force                  :强制删除
  -h, --help                   :当前命令使用说明
      --include <string>       :包含当前通配符的文件名
      --parallel <int>         :批量删除并发数，默认值为10
  -q, --qps <int>              :限制每秒请求数量，默认50qps，取值范围：1~1000
  -r, --recursive              :递归文件夹中的所有文件及子目录下所有文件
      --reduce 		       :是否以精简模式执行（不展示进度）
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

- 删除sync产生的缓存

```
./us3cli rm -f --cache sync
```

## ls

该命令用于列出存储空间(Bucket)、对象（Object）或ProjectID

### 命令格式

```
us3cli ls [us3://<桶名称>] [--limit <输出限制数>][--restore][--flat][--mimetype][--etag][--project][--projectid <项目ID>]
```

### 参数说明

```
      --accesskey <string>   :用于访问us3的API公钥或Token公钥
      --config <string>      :当前命令临时指定配置名/配置文件路径
      --endpoint <string>    :固定域名，可通过地域和域名页查看
      --etag                 :是否展示etag信息
      --flat                 :是否以非层级结构（展开所有目录）显示,默认层级显示
  -h, --help                 :当前命令使用说明
  -l, --limit <int>          :需要同时列出的最大文件条数，默认值为100
      --mimetype             :是否展示mimetype信息
      --project              :列出当前账户下所有项目及项目ID
      --projectid <string>   :列出bucket时指定projectid，默认项目可忽略
  -r, --restore              :是否展示数据解冻信息
      --secretkey <string>   :用于访问us3的API私钥或Token私钥
```

注意：

ls列取文件列表默认情况修改为只显示文件key，文件大小，存储类型，创建时间，可以自行通过参数添加restore、etag、mimetype等信息

### 使用示例

- 列出当前权限下所有bucket

```
./us3cli ls
BucketName      Region  Acl     CreateTime         
bucket1     	cn-bj   public  2020-03-27 17:20:56
bucket2      	cn-bj   private 2020-03-27 17:20:43
us3cli          cn-bj   public  2020-09-15 16:17:24
```

- 列出bucket下所有对象

```
./us3cli ls us3://bucket
Key                     FileSize   StorageClass         Etag                             CreateTime      
us3://bucket/cf1   	DIR        IA                   AQAAANo5o-5ea0sNMlW_75VgGJCv2AcJ 2020-07-16 18:51:04  
us3://bucket/aa.txt     4KB        STANDARD             AQAAAGNlfLt9cIbFawzU5caZm7aDZkho 2020-07-22 11:04:34 
us3://bucket/aa.txt     4KB        ARCHIVE              AQAAAGNlfLt9cIbFawzU5caZm7aDZkho 2020-07-22 11:04:34 
```

- 列出当前账号下所有project

```
./us3cli ls --project
当前账号下项目信息如下：
No.     ProjectName                     ProjectId 
1       Default                         org-ddhm4o
2     	Test         			org-nhrc4y
3       Test2       			org-wr5pej
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
      --storageclass <string>  :指定存储类型,对应有效值：STANDARD, IA, ARCHIVE
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

1.修改test.txt文件的元数据信息，以key=value形式作为参数，可修改多个元数据信息，中间以英文逗号","分隔

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
us3cli restore us3://<桶名字>/<文件Key> [--recursive][--reduce][--qps <每秒请求数上限>][--exclude <通配符表达式>][--rexclude <正则表达式>][--include <通配符表达式>][--rinclude <正则表达式>][--parallel <批量激活并发数>]
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
      --reduce 		       :是否以精简模式执行（不展示进度）
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
[-]     SmbCbzsQA0lAOktMSbGyNHv5HQ0=
```

- 计算多个文件etag

```
cat test4.txt | ./us3cli etag us3://bucket1/test.txt us3://bucket1/test2.txt test3.txt -

Name                                    Etag                            
us3://bucket1/test.txt                  AQAAAEpmwm87EANJQDpLTEmxsjR7-R0N
us3://bucket1/test2.txt                 AQAAAEpmwm87EANJQDpLTEmxsjR7-R0N
test3.txt    				AQAAAHPuBl-6VRpzVHiBFjSOVhLrcsam
[-]                                     SmbCbzsQA0lAOktMSbGyNHv5HQ0= 
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

us3cli version 1.2.0
```
