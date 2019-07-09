{{indexmenu_n>1}}

## UFile旧版计费规则（2018年12月1日正式失效）

**UFile产品价格**

<table>
<tbody>
<tr class="odd">
<td>计费项</td>
<td>地域</td>
<td>范围</td>
<td>价格</td>
</tr>
<tr class="even">
<td>存储容量</td>
<td>北京、香港、广东、上海二、<br />
洛杉矶、新加坡、雅加达</td>
<td>All</td>
<td>0.0052元/GB/天</td>
</tr>
<tr class="odd">
<td>:::</td>
<td>拉各斯、台北</td>
<td>All</td>
<td>0.004元/GB/天</td>
</tr>
<tr class="even">
<td>:::</td>
<td>全球化</td>
<td>All</td>
<td>0.0091元/GB/天</td>
</tr>
<tr class="odd">
<td>上传流量</td>
<td>北京、香港、广东、上海二、<br />
洛杉矶、新加坡、雅加达、<br />
拉各斯、台北、全球化</td>
<td>All</td>
<td>免费</td>
</tr>
<tr class="even">
<td>下载流量</td>
<td>北京、香港、广东、<br />
上海二、洛杉矶、全球化</td>
<td>0—1TB</td>
<td>0.45元/GB</td>
</tr>
<tr class="odd">
<td>:::</td>
<td>:::</td>
<td>1TB—10TB</td>
<td>0.40元/GB</td>
</tr>
<tr class="even">
<td>:::</td>
<td>:::</td>
<td>10TB—50TB</td>
<td>0.36元/GB</td>
</tr>
<tr class="odd">
<td>:::</td>
<td>:::</td>
<td>50TB—100TB</td>
<td>0.32元/GB</td>
</tr>
<tr class="even">
<td>:::</td>
<td>:::</td>
<td>100TB以上</td>
<td>0.28元/GB</td>
</tr>
<tr class="odd">
<td>:::</td>
<td>新加坡、雅加达</td>
<td>0—1TB</td>
<td>0.65元/GB</td>
</tr>
<tr class="even">
<td>:::</td>
<td>:::</td>
<td>1TB—10TB</td>
<td>0.60元/GB</td>
</tr>
<tr class="odd">
<td>:::</td>
<td>:::</td>
<td>10TB—50TB</td>
<td>0.54元/GB</td>
</tr>
<tr class="even">
<td>:::</td>
<td>:::</td>
<td>50TB—100TB</td>
<td>0.48元/GB</td>
</tr>
<tr class="odd">
<td>:::</td>
<td>:::</td>
<td>100TB以上</td>
<td>0.42元/GB</td>
</tr>
<tr class="even">
<td>:::</td>
<td>拉各斯、台北</td>
<td>All</td>
<td>0.4元/GB</td>
</tr>
<tr class="odd">
<td>请求次数</td>
<td>北京、香港、广东、上海二、<br />
洛杉矶、新加坡、雅加达、拉各斯、<br />
台北、全球化</td>
<td>All</td>
<td>0.01元/万次</td>
</tr>
<tr class="even">
<td>海外专线流量</td>
<td>无</td>
<td>All</td>
<td>4元/GB</td>
</tr>
<tr class="odd">
<td>UFile流量为阶梯定价，计价示例如下：<br />
例1:购买北京地域流量20TB，则所需支付金额为7372.8元=0.36元/GB *(20 *1024)GB<br />
例2:购买新加坡地域流量60TB，则所需支付金额为29491.2元=0.48元/GB *(60 *1024)GB</td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table>

提示：
1、全球化空间管理与单地域空间管理配额不互通
2、上传请求量按请求次数计费

## 计费说明

对象存储按照存储容量、下载流量和请求次数三个维度进行计费。

### 存储容量计费说明

UCloud为用户提供每天20
GB免费存储容量，免费存储容量使用结束后，请购买存储容量，存储容量按天进行结算与扣减。购买时会自动四舍五入取整到100GB\*天。

### 下载流量计费说明

UCloud为用户提供每月20 GB免费下载流量，免费下载流量使用结束后，请购买下载流量，下载流量按天进行结算与扣减。

下载流量包括通过CDN加速域名(默认生成的域名和自定义域名)访问和直接访问源站所产生的流量。  
注：通过云主机内网访问所产生的流量不收费。

### 请求次数计费说明

UCloud为用户提供每月200万次免费请求次数，免费请求次数使用结束后，请购买请求次数，请求次数按天进行结算与扣减。

请求次数仅计算访问源站所产生的请求次数。

### 海外专线流量计费说明

海外专线服务默认关闭，需要开通的客户请参考：[增值服务](https://docs.ucloud.cn/storage_cdn/ufile/pic)

UCloud将会按月统计用户因专线使用所产生的专线流量，于第二个自然月月初统一结算。海外专线流量价格请参考价格总表。

### CDN加速域名计费提醒

通过默认生成的CDN加速域名和绑定的自定义CDN域名访问所产生的流量都计入UFile的下载流量，在UFile产品中进行结算，请勿与UCDN产品计费混淆。

注：
在UCDN产品中申请加速域名并指向UFile源站的情况，需要到bucket下绑定自定义域名才可正常使用。
此情形下回源流量计费并在UFile下载流量中结算；CDN使用量在UCDN产品中结算。请勿混淆。
