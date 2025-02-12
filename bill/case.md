

# 计费案例

## 案例一
用户A 使用国内 US3，同时开启了 UCDN 加速服务。在 18年12月1号 的日消费量：文件存储（标准类型）容量为 2.5TB，产生 CDN 回源流量200GB。 该用户的加速场景产生了 CDN 下载流量 900GB。  
当天费用计算：  
US3 产品侧总费用如下：  
存储空间费用：2.5 \* 1024GB  \* 0.004 元/GB/天 = 10.24 元  
CDN 回源流量费用：200GB \* 0.15 元/GB = 30 元  
总费用：10.24 + 30 = 40.24 元  
UCDN 产品侧总费用如下：  
CDN 下载流量费用= 900GB \* 0.34 元/GB = 306 元  
US3+UCDN 总费用：40.24 + 306 = 346.24 元  

备注：用户因为开启了 UCDN 加速服务，还会生成一项 CDN 下载流量的费用，在 UCDN 产品侧统计和计费。计费价格详见：[UCDN 产品价格](https://docs.ucloud.cn/ucdn/charge)。

## 案例二
用户B 使用 US3，没有开启 UCDN 加速服务。在 18年12月1号 的日消费量：文件存储（标准类型）容量为 2.5TB，在闲时段产生外网流出流量 150GB，在忙时段产生外网流出流量 800GB。  
当天费用计算：  
存储空间费用：2.5\* 1024 GB\* 0.004 元/GB/天 = 10.24 元  
外网流出流量费用：150GB \* 0.25 元/GB + 800GB \* 0.45 元/GB = 397.5 元  
总费用：10.24 + 397.5 = 407.74 元

## 案例三
用户C 使用 US3，没有开启 UCDN 加速服务。在 18年12月1号 的日消费量：文件存储（标准类型）容量为 1TB，文件存储（低频类型）容量为 1TB，文件存储（归档类型）容量为 0.5TB。在忙时段产生标准类型外网流出流量 500GB，低频类型外网流出流量 10GB，归档类型解冻数据量 15GB，归档类型外网流出流量 10GB。  
当天费用计算：  
存储空间费用：1 \* 1024GB \* 0.004 元/GB/天 + 1 \* 1024GB \* 0.002 元/GB/天 + 0.5 \* 1024GB  \* 0.0008 元/GB/天 = 10.24 元  
外网流出流量费用：（500GB + 10GB + 10GB）\* 0.45 元/GB = 234 元  
低频类型取回费用：10GB \* 0.03 元/GB = 0.3 元
归档类型解冻费用：15GB \* 0.06 元/GB = 0.9 元（注：如执行解冻操作，即使数据未被取回也会收取数据解冻费用）
总费用：10.24 + 234 + 0.3 + 0.9 = 245.44 元
