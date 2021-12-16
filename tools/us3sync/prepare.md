# 下载与环境准备

US3SYNC提供“通用部署”和“UHOST镜像启动”两种部署US3SYNC的方式。用户可根据现有资源情况选择“通用部署”或“UHOST镜像启动”US3SYNC服务。通用部署方案如下：

## 运行环境

- Linux：
  - CentOS 7.0 及以上 (可通过`cat /etc/redhat-release`查看)
  - Ubuntu 16.04 及以上 (可通过`cat /etc/issue`查看)

US3SYNC 依赖 telnet，expect，rsync 命令，确保预装这些命令，使用 yum 源做包管理可以使用以下命令安装：

```
yum install -y telnet expect rsync
```

## 下载并解压

```
wget -O US3SYNC.tgz "https://ufile-release.cn-bj.ufileos.com/US3SYNC/US3SYNC.tgz" 
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

1. 服务启动后，在浏览器中打开：https://<web服务监听IP>:<web服务监听端口>/<br>**注：使用云主机部署迁移服务时，这里需要使用EIP，而不是0.0.0.0。**
2. 页面登录，使用启动时设置的用户名和密码。
3. 添加工作节点，参考 [创建节点界面说明](/ufile/tools/us3sync/quickaccess?id=创建节点界面说明)，需要为每个节点提供唯一工作路径。
   每个工作节点需要提供唯一工作路径，如果路径不存在会自动创建对应目录。<br>**注：建议使用内网ip。**

## UHOST镜像启动US3SYNC

US3SYNC提供快速拉起环境的云主机镜像，本节介绍如何使用UHost镜像直接启动一个运行US3SYNC的主节点。

### 操作步骤

登录UHost控制台的[创建主机界面](https://console.ucloud.cn/uhost/uhost/create)。

在创建主机界面配置好各种参数后，点击右下角的等效CLI命令行，如图:

![](http://ufile-release.cn-bj.ufileos.com/us3sync/doc/cli_command.png)

在弹出页面中点击在CloudShell中运行按钮:

![](http://ufile-release.cn-bj.ufileos.com/us3sync/doc/run_cloudshell.png)

将弹出的命令行中的命令的--image-id选项对照下方附录中的表格，根据可用区替换成对应的镜像id:

![](http://ufile-release.cn-bj.ufileos.com/us3sync/doc/replace_image_id.png)

最后按回车执行命令，等待云主机创建。您可以在[Uhost控制台](https://console.ucloud.cn/uhost/uhost)查看云主机的创建和启动情况。

待云主机启动完毕，您可以在浏览器上访问主机外网ip对应的https服务，首次登录会要求您输入用户名和密码(如果报错服务器拒绝访问，请您稍等片刻，US3SYNC需要一点时间来启动):

![](http://ufile-release.cn-bj.ufileos.com/us3sync/doc/user_passwd.png)

### 镜像启动可支持地域

镜像列表（表格中未列出的区域暂不提供镜像支持）：

| 可用区                | 镜像id        |
| :-------------------- | :------------ |
| cn-bj2 北京二B        | uimage-shd2rl |
| cn-bj2 北京二C        | uimage-o55evb |
| cn-bj2 北京二D        | uimage-ty5c13 |
| cn-bj2 北京二E        | uimage-fs4rf2 |
| cn-sh2 上海二A        | uimage-r1du3i |
| cn-sh2 上海二B        | uimage-slmgrf |
| cn-sh2 上海二C        | uimage-b0zq42 |
| cn-sh 上海金融云A     | uimage-smfk3r |
| cn-gd 广州B           | uimage-g1oh45 |
| jpn-tky 东京A         | uimage-iushr2 |
| us-ca 洛杉矶A         | uimage-g5y5kn |
| bra-saopaulo 圣保罗A  | uimage-0w3bob |
| vn-sng 胡志明A        | uimage-xr05sj |
| tw-tp 台北A           | uimage-vkrfzb |
| hk 香港A              | uimage-nxwznl |
| hk 香港B              | uimage-iayhtb |
| ind-mumbai 孟买A      | uimage-bai4bw |
| idn-jakarta 雅加达A   | uimage-04m5os |
| kr-seoul 首尔A        | uimage-nrgrdw |
| rus-mosc 莫斯科A      | uimage-4km1pq |
| afr-nigeria 尼日利亚A | uimage-krn5jt |
| sg 新加坡A            | uimage-kzjfo1 |
| th-bkk 曼谷A          | uimage-vxedo0 |
| th-bkk 曼谷B          | uimage-s0iu3x |
| ge-fra 法兰克福A      | uimage-bslgo1 |
| us-ws 华盛顿A         | uimage-u4ejqf |
| ph-mnl 马尼拉A        | uimage-kd1jjd |

## 视频参考

部署与使用视频教学请参考[【快速上手】](/ufile/tools/us3sync/quickaccess?id=视频教学
)
