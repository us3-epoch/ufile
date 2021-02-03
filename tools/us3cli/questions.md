# 常见问题

## Bucket操作如stat、ls、du报错 bucket not found

### 问题原因

当前工具查找bucket范围默认为当前账户默认项目，如您的Bucket不在默认项目下，普通操作是无法找到的，需要使用--projectid选项填写您要操作的bucket所在项目ID，即projectid。

### 解决方案

在您想要操作的命令后加上--projectid <projectid> 就可以操作当前项目下的bucket

```
#1.查看projectid
#方法1：mb命令创建时会展示当前账户所有projectid
#方法2：登录控制台查看左上角项目ID
#方法3: ./us3cli ls --projectid 展示当前账户下所有projectid

#2.指定projectid操作bucket
./us3cli stat us3://bucketest --projectid xxxx
./us3cli du us3://buckettest --projectid xxxx
```

## 并发数parallel和qps有什么区别？

parallel代表同时进行任务的协程数，不保证具体的请求速率。

qps代表每秒请求数量限制，如qps为1，那么该请求会限制在1秒1个。

## 上传文件不指定文件key，上传成功但找不到文件

### 问题原因

在使用us3cli时，需要将文件上传到文件夹内，由于对象存储可以存在同名文件和文件夹的特性，所以需要在文件夹名称后加上“/”才可以判断为上传文件至当前文件夹，否则会识别为普通上传文件。

### 解决方案

如：上传本地文件test1至存储空间bucketTest的test目录下时，以下方式1只会生成一个test文件在根目录下，而方式2和方式3都可以成功上传文件到test下，命名为test1。

```
方式1：./us3cli cp test1 us3://bucketTest/test
方式2: ./us3cli cp test1 us3://bucketTest/test/test1
方式3: ./us3cli cp test1 us3://bucketTest/test/
```

## 将本工具放入bin目录下全局使用时，使用update命令提示找不到文件的问题

### 问题原因

更新时工具会根据输入参数找到可执行文件位置，但参数中只有命令名，没有可执行文件路径，故出现找不到文件的问题。

### 解决方案

在bin目录下执行us3cli更新命令。
