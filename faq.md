# FAQ



## 对象存储空间和Key是什么？

对象存储空间（简称存储空间）是文件的组织管理单位，一个文件必然位于某个空间中。空间名称全局唯一，且无法进行修改。

文件名是对应文件的名称，在存储空间中全局唯一，每个文件名在存储空间均标识了一个文件，写入文件时，用户可以自定义文件名。上传同样文件名的文件，会导致原文件名文件被覆盖。

## 公共空间和私有空间有何区别？

公共空间指任何人都可以直接通过URL访问该空间里的文件，不需要授权签名。

私有空间需要根据API公私钥生成正确的签名才能访问文件。

## 如何查看及管理上传的文件？

用户可以通过控制台的文件管理页查看已上传的文件，同时也可以使用文件管理工具或API查看已经上传的文件。

## 是否支持目录、文件列表？

对象存储空间没有目录的概念，所以不能按照目录列出文件列表。

但上传文件时，Key依然沿用目录形式，便于特定的用户使用场景需要。

例如：demobucket.ufile.ucloud.cn/test/a.jpg 这里的 key=test/a.jpg 。

## 如何使用对象存储空间提供的两个域名？

每个存储空间默认提供一个存储空间域名与一个CDN加速域名。

文件上传操作必须将请求发往存储空间域名。

文件下载操作可以通过访问存储空间域名或CDN加速域名进行。文件下载时建议使用CDN加速域名进行下载，以获得更好的下载体验。

## 对象存储空间是否有容量上限？

对象存储空间容量没有上限，按需使用。

## 文件大小有什么限制？

单个文件大小上限为5TB。

## 对象存储空间如何通过内网访问？

1、空间管理的API，内网访问时域名同公网，使用api.ucloud.cn。

2、文件管理的API，需要使用内网专用域名\<bucket\_name\>.ufile.cn-north-03.ucloud.cn。

例如，bucket名称为demobucket，则其C机房内网域名为demobucket.ufile.cn-north-03.ucloud.cn。

3、文件管理的API列表如下：PutFile，PostFile，UploadHit，GetFile，DeleteFile，InitiateMultipartUpload，UploadPart，FinishMultipartUpload，AbortMultipartUpload。

4、命令行工具，通过内网访问，需要将配置文件中的proxy_host 改成 "proxy_host":
"www.ufile.cn-north-03.ucloud.cn"。

5、SDK（以phpSDK为例）通过内网访问，需要将配置文件中$UCLOUD\_PROXY\_SUFFIX改成$UCLOUD\_PROXY\_SUFFIX
= '.ufile.cn-north-03.ucloud.cn' （其他SDK的配置文件通常改proxy_suffix)。

## 各机房proxy_host地址分别是什么？

北京  
外网：www.cn-bj.ufileos.com  
B机房内网：www.ufile.cn-north-02.ucloud.cn  
C机房内网: www.ufile.cn-north-03.ucloud.cn  
D机房内网: www.ufile.cn-north-04.ucloud.cn  
上海二  
外网: www.cn-sh2.ufileos.com  
内网: www.internal-cn-sh2-01.ufileos.com  
香港  
外网：www.hk.ufileos.com  
内网：www.internal-hk-01.ufileos.com  
广州  
外网：www.cn-gd.ufileos.com  
内网：www.internal-cn-gd-02.ufileos.com  
洛杉矶  
外网：www.us-ca.ufileos.com  
内网：www.internal-us-ca-01.ufileos.com  
雅加达  
外网：www.idn-jakarta.ufileos.com  
内网：www.internal-idn-jakarta-01.ufileos.com  
新加坡  
外网：www.sg.ufileos.com  
内网：www.internal-sg-01.ufileos.com  
尼日利亚：  
外网：www.afr-nigeria.ufileos.com  
内网：www.internal-afr-nigeria.ufileos.com  
台北：  
外网：www.tw-tp.ufileos.com  
内网:www.internal-tw-tp.ufileos.com  
圣保罗：  
外网：www.bra-saopaulo.ufileos.com  
内网：www.internal-bra-saopaulo.ufileos.com  
迪拜：  
外网：www.uae-dubai.ufileos.com  
内网：www.internal-uae-dubai.ufileos.com  
越南：  
外网：www.vn-sng.ufileos.com  
内网：www.internal-vn-sng.ufileos.com  
孟买：  
外网：www.ind-mumbai.ufileos.com  
内网：www.internal-ind-mumbai.ufileos.com   
华盛顿：  
外网：www.us-ws.ufileos.com   
内网：www.internal-us-ws.ufileos.com   
法兰克福：  
外网：www.ge-fra.ufileos.com   
内网：www.internal-ge-fra.ufileos.com   

##  使用filemgr工具时遇到timeout错误时怎么办？

"client timeout" 、"i/o
timeout"，文件所在机器的出带宽有限很容易发生此错误，可以使用mput，并增加--speedlimit进行限速。

## UFile域名被第三方平台告知有安全风险怎么处理？

第三方平台的安全检测是基于泛域名做检测及封禁处理，而UFile不同客户的域名使用同一个泛域名，因此只要有一个客户有违规内容，整个UFIle默认域名都会被安全软件封禁。

这个问题我们和第三方平台沟通过多次，对方以按照逐个域名做封禁成本太大为由拒绝。因此遇到封禁情况的客户，建议通过使用自定义域名解决。配置方法参考\[操作指南\]—\[自定义域名\]。

## 有跨域需求时如何申请？

如需在UFile配置跨域，需要指派工单至技术支持，工单内注明：bucket名称、ufile域名、Origin地址和要跨域的http
method。

## CDN回源UFile的流量如何计费？

CDN回源，流量从UFile流向UCDN，这部分流量UCDN不计费，由UFile计费，请参照下图：
![](/images/cdn回源ufile的流量.png)  
计费价格详见：[UFile产品价格](https://cms.docs.ucloudadmin.com/storage_cdn/ufile/bill)

## 镜像回源支持的空间类型

镜像回源没有签名的过程，目前只支持公开空间。

## 欠费处理

1.欠费3天后服务（上传、下载、删除等）会被限制，部分请求会失败，直至所有服务都不可用。限制服务期间存储数据仍被保留。  
2.欠费30天后回收存储空间和删除存储数据。欠费3天内支付欠费订单，不受影响；欠费3-30天之间，支付全部欠费订单，恢复服务可用性，解除限购。  
3.针对七天回收以及立即回收用户，UFile欠费3天，开始限购，项目下有UFile欠费，则该项目不可新建UFile存储空间。  
![](/images/ufile欠费处理弹窗.png)
![](/images/ufile欠费限购.png)

4.欠费后、限制服务前、删除前/后都会有邮件通知和短信通知，被通知人为对应项目的资源回收消息订阅人。  
**欠费处理时间轴如下：**  
![](/images/欠费通知时间轴.png)

