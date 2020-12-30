

# 一致性比对工具

## 工具使用

1. 下载对应的check工具包：

```
linux64 	http://fail-check.cn-bj.ufileos.com/check-client-linux64.zip
mac		http://fail-check.cn-bj.ufileos.com/check-client-mac.zip 
```

2. 解压工具包，进入目录，执行以下命令为工具附加可执行权限：

```
chmod +x chmod.sh
./chmod.sh
```

3. 使用./get_file_list.sh拉取有问题的文件列表

```
./get_file_list.sh <要校验的bucket名字> <要校验的本地文件目录> <要校验的us3目录>

执行以上命令产生下列文件：
checkData.txt 		：存放四种可疑文件列表
invalid_content.txt : 存放etag不一致或size不一致的文件列表
local_not_exists.txt: 存放相对us3目录，本地不存在的文件列表
new_files.txt		: 存放相对本地目录，us3不存在的文件列表

//checkData.txt中四种情况区分
[1]表示us3文件不存在
[2]表示本地文件不存在
[3]表示文件大小不一致
[4]表示etag不一致
```

4. 如果需要重新上传etag以及size不一致的批量文件，执行以下命令

```
./upload_invalid_content.sh <要上传的bucket>
```

5. 如果需要上传us3缺少的文件，执行以下命令

```
./upload_new_files.sh <要上传的bucket>
```

6. 上传完成后重新校验

```
./check.sh <要校验的bucket名字> <要校验的本地文件目录> <要校验的us3目录>
```

## checkData 文件内容解析

1. us3文件不存在

```
[1],us3 file does not exist,本地文件名，us3文件名
```

2. 本地文件不存在

```
[2],local file does not exist,us3文件名
```

3. 文件大小不一致

```
[3],inconsistent file size,本地文件名,文件大小,us3文件名,文件大小
```

4. etag不一致

```
[4],inconsistent etag,本地文件名,文件etag,us3文件名,文件etag
```

#### 