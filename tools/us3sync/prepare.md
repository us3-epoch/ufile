# 下载与环境准备

## 运行环境

US3SYNC 支持 linux 环境部署。

US3SYNC 依赖 telnet，expect，rsync 命令，确保预装这些命令，使用 yum 源做包管理可以使用以下命令安装：

```
yum install -y telnet expect rsync
```

## 下载并解压

```
wget -O US3SYNC.tgz "http://ufile-release.cn-bj.ufileos.com/US3SYNC/US3SYNC.tgz" 
tar xzf US3SYNC.tgz
cd ./US3SYNC
```

## 启动master服务

```
./console.sh start
请设置缓存服务监听端口[9000]:
请设置缓存服务密码[user-passwd]:
# 云主机一般绑定EIP，建议ip地址使用0.0.0.0
请设置web 服务监听地址[0.0.0.0:443]:
# 内部通信地址不提供外网服务，建议ip地址使用机器内网ip
请设置内部通信监听地址[x.x.x.x:8080]:
请设置报错重试次数[10]:
请设置web 登录使用的用户名[root]: 
请设置web 登录使用的密码[passwd]: 
US3SYNC start success!
# 查看master服务
./console.sh show
# 结束master服务
./console.sh stop
# 验证
# 查看进程是否正常启动
./console.sh show
```

## 添加worker节点

### 界面操作

1. 服务启动后，在浏览器中打开：https://<web服务监听IP>:<web服务监听端口>/<br>**注：使用云主机部署迁移服务时，这里需要使用EIP，而不是0.0.0.0。**
2. 页面登录，使用启动时设置的用户名和密码。
3. 添加工作节点，参考 [创建节点界面说明](/ufile/tools/us3sync/quickaccess?id=创建节点界面说明)，需要为每个节点提供唯一工作路径。
   每个工作节点需要提供唯一工作路径，如果路径不存在会自动创建对应目录。<br>**注：建议使用内网ip。**

### 命令行操作

```
# 部署worker节点，可以部署多个，每个节点需要一个单独目录
mkdir -p node/bin node/log
cp bin/worker/US3SYNC ./node/
# 启动worker节点
cd node
nohup ./bin/US3SYNC -S <speed limit, MB> -Q <qps limit, per second> <master-inner-ip>:<master-inner-port> --cache-addr <ip:port> --cache-pass <password> > log/worker.log 2>&1 &
# 验证
# 查看进程是否正常启动
ps -ef | grep US3SYNC
```