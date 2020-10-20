# 数据库备份方案

## 背景
对象存储作为海量非结构化数据的云存储应用，面对日益上涨的数据库备份场景，可以有效帮助用户缩减备份流程，降低备份成本，有效提升用户体验。
本文介绍了如何基于 US3 完成数据库备份。

## 应用场景
目前 US3 数据库备份场景主要有以下三类：

1. 备份与恢复：备份方式推荐使用 Filemgr 备份到 US3 中。
Filemgr 支持本地备份恢复与流式备份恢复，通过流式功能可以帮助用户完成数据不落地备份与恢复。

2. 分级存储：针对需要定时清理备份、缩减备份成本的用户，US3 支持生命周期功能。
通过控制台指定生命周期规则，可以帮助用户完成：1、定期清理；2、定期转入低频；3、定期转入归档；

3. 异地备份：针对需要更高安全级别的用户，US3 支持跨区域复制功能。
通过控制台配置跨区域复制功能，可以帮助用户在上传备份的同时，完成数据的异地备份。

![image](/images/backup1.png)

## 方案优势
1. 使用 Filemgr 进行流式备份以及流式恢复，完成不落地备份与恢复，可以避免落盘操作。

2. 使用 US3 [生命周期](/ufile/guide/lifecycle) 功能，配合定期删除、低频存储、归档存储可以实现数据分级存储，帮助用户节约存储成本。

3. 使用 US3 [跨区域复制](/ufile/guide/multisite)，为备份数据进行异地容灾，提高备份数据安全性。

## 方案实施
### 使用Filemgr进行流式备份，流式恢复
1. 下载 Filemgr，[迁移工具](ufile/tools/tools/tools_file)

2. 在 Filemgr 目录下配置 config.cfg，proxy host 域名请参照 [地域和域名](/ufile/introduction/region)

```
{
    "public_key" : "paste your public key here",
    "private_key" : "paste your private key here",
    "proxy_host" : "www.cn-bj.ufileos.com",       // proxy host请填写对应地域域名
    "api_host" : "api.spark.ucloud.cn"
}
```
3. 使用 Filemgr 进行备份恢复，此处展示最简命令，其他备份命令请结合自己业务类比实现

```
bash
# 注意如果，欲使用低频存储(IA)或者冷存储(ARCHIVE)，请在命令参数storageclass中指定，支持三种值：STANDARD, IA, ARCHIVE
# 注意如果，备份时指定了storageclass参数为ARCHIVE，需要提前对该文件restore
./filemgr-linux64 --action restore --bucket <bucketName> --key <backupKey>
 
# 逻辑备份
    # 全库备份
    mysqldump -A | ./filemgr-linux64 --action stream-upload --bucket <bucketName> --key <all-backupKey> --file stdin --threads <threads> --retrycount <retry> --storageclass <storage-class>
    # 分库备份
    mysqldump -B database1 database2 | ./filemgr-linux64 --action stream-upload --bucket <bucketName> --key <part-backupKey> --file stdin --threads <threads> --retrycount <retry> --storageclass <storage-class>
 
# 逻辑备份恢复
    # 全库备份恢复
    ./filemgr-linux --action stream-download --bucket <bucketName> --key <all-backupKey> --threads <threads> --retrycount <retry> 2>./error.log | mysql
    # 分库备份恢复
    ./filemgr-linux --action stream-download --bucket <bucketName> --key <part-backupKey> --threads <threads> --retrycount <retry> 2>./error.log | mysql
 
# ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# xtrabackup物理备份
    # 全量备份
    innobackupex --stream=tar | ./filemgr-linux64 --action stream-upload --bucket <bucketName> --key <full-backupKey> --file stdin --threads <threads> --retrycount <retry> --storageclass <storage-class>
    # 增量备份
    innobackupex --stream=tar --extra-lsndir=/data/backup/chkpoint /data/backup/tmp/ | ./filemgr-linux64 --action stream-upload --bucket <bucketName> --key <incre-backupKey-base> --file stdin --threads <threads> --retrycount <retry> --storageclass <storage-class> # 全备
    innobackupex --stream=xbstream --incremental --extra-lsndir=/data/backup/chkpoint --incremental-basedir=/data/backup/chkpoint /data/backup/tmp/ | ./filemgr-linux64 --action stream-upload --bucket <bucketName> --key <incre-backupKey-incre> --file stdin --threads <threads> --retrycount <retry> --storageclass <storage-class> # 增备
 
# xtrabackup物理备份恢复, 需要提前转移原来的DB数据, 备份恢复后需要重启服务
    # 全量备份恢复
        # full-backupKey为全量备份使用的key：
        ./filemgr-linux --action stream-download --bucket <bucketName> --key <full-backupKey> --threads <threads> --retrycount <retry> 2>./error.log | tar xf - -C /data/backup/full/
        innobackupex --apply-log /data/backup/full/
        innobackupex --copy-back --rsync /data/backup/full/
 
    # 增量备份恢复
        # full-backupKey为全量备份使用的key，incre-backupKey为增量备份使用的key：
        ./filemgr-linux --action stream-download --bucket <bucketName> --key <incre-backupKey-base> --threads <threads> --retrycount <retry> 2>./error.log | tar xf - -C /data/backup/base/
        innobackupex --apply-log --redo-only /data/backup/base/
        ./filemgr-linux --action stream-download --bucket <bucketName> --key <incre-backupKey-incre> --threads <threads> --retrycount <retry> 2>./error.log | xbstream -x -C /data/backup/incre
        innobackupex --apply-log /data/backup/base --incremental-dir=/data/backup/incre
        innobackupex --copy-back --rsync /data/backup/base
 
# lvm snapshot物理备份恢复
    # 备份，/snap-lvm0为备份lv 挂载点，/data-lvm0为源lv挂载点
    tar czf - /snap-lvm0/* | ./filemgr-linux64  --action stream-upload --bucket <bucketName> --key <lvmsnap-backupKey> --file stdin
    # 恢复，本地数据被清空，--strip-components 用来去除压缩快照时产生的第一层目录
    ./filemgr-linux64 --action stream-download --bucket <bucketName> --key <lvmsnap-backupKey> 2>./error.log | tar xzf - -C /data-lvm0/ --strip-components 1
    # 合并历史快照, /snap-lvm0为快照lv挂载点
    ./filemgr-linux64 --action stream-download --bucket <bucketName> --key <lvmsnap-backupKey> 2>./error.log | tar xzf - -C /snap-lvm0/ --strip-components 1
    lvconvert --merge <vg>/<snap-lv>
针对有特殊需求的用户，这边提供了相应的逻辑备份命令，其他备份命令请类比实现：

# 压缩
    # 备份
    mysqldump -A | gzip | ./filemgr-linux64 --action stream-upload --bucket <bucketName> --key <all-backupKey> --file stdin --threads <threads> --retrycount <retry> --storageclass <storage-class>
    # 恢复
    ./filemgr-linux --action stream-download --bucket <bucketName> --key <all-backupKey> --threads <threads> --retrycount <retry> 2>./error.log | gzip -d | mysql
 
# 加密
    # 备份，使用aes256，指定密码文件key file在备份路径中进行压缩
    mysqldump -A | openssl enc -e -aes256 -in - -out - -kfile <key file> | ./filemgr-linux64 --action stream-upload --bucket <bucketName> --key <all-backupKey> --file stdin --threads <threads> --retrycount <retry> --storageclass <storage-class>
    # 恢复
    ./filemgr-linux --action stream-download --bucket <bucketName> --key <all-backupKey> --threads <threads> --retrycount <retry> 2>./error.log | openssl enc -d -aes256 -in - -out - -kfile <key file> | mysql
```

**备注：**
如果不希望异常情况终止任务，请将 retrycount 参数设置为一个比较大的值，默认为 10。每次执行失败会开始重试，第 5 次重试开始每次重试会等待 5s，请合理计算重试次数。

### 使用生命周期实现定期删除
1. 打开对象存储控制台，进入备份使用的 bucket 详情页

![image](/images/backup2.png)

2. 点击生命周期 tab，进入生命周期配置页

![image](/images/backup3.png)

3. 为该 bucket 配置定期删除任务

4. 当备份文件超过配置期限，文件会被自动删除

### 使用跨区域复制进行异地容灾
1. 打开对象存储控制台，进入备份使用的 bucket 详情页

![image](/images/backup4.png)

2. 点击开区域复制 tab，进入跨区域复制配置页

![image](/images/backup5.png)

3. 为该 bucket 配置跨区域复制任务

4. 当该 bucket 下产生备份文件时，文件会被自动同步到配置好的异地 bucket 中
