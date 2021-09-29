# 快速上手

- [使用方式](#使用方式)
  - [登录](#登录)
  - [主界面](#主界面)
  - [创建目录](#创建目录)
  - [上传文件/文件夹](#上传文件文件夹)
  - [下载](#下载)
  - [更多操作](#更多操作)
  - [历史记录](#历史记录)
  - [文件\文件夹重命名](#文件文件夹重命名)
- [账号管理](#账号管理)
- [参数设置](#参数设置)


## 使用方式
打开可执行文件，双击安装好的桌面应用程序

### 登录

双击打开图标进入应用界面，Name选项只用来做标识，Endpoint可以选择外网、内网以及自定义接入点，需要填入bucket绑定令牌的公钥和私钥
![image](/images/us3Browser/login.png)

### 主界面
主界面主要分为6部分：
  1.目录结构，对应自己账号下bucket的文件夹
  2.账号管理以及设置的基本功能
  3.前进、后退、刷新快捷键和基本的文件目录操作区
  4.当前bucket下所有的文件以及目录的展示区
  5.对应当前文件或目录的下载、下载链接复制以及更多操作的按键
  6.自己操作的任务、上传以及下载的历史记录
![image](/images/us3Browser/main.jpg)
### 创建目录
单击创建目录按钮，会弹出对话框，输入目录名，点击确定即可
![image](/images/us3Browser/createdir.jpg)
![image](/images/us3Browser/directory.jpg)
### 上传文件/文件夹
点击上传，会出现文件和文件夹两个选项，依据需求选择，会弹出选择本地文件窗口，文件可以同时上传多个文件，文件夹一次只能上传一个
![image](/images/us3Browser/upload.jpg)
![image](/images/us3Browser/upload1.jpg)
![image](/images/us3Browser/upload2.jpg)
### 下载
* 单个文件或目录下载，可以通过复制url，在网站进行下载，或者直接点击下载，选择保存的下载路径即可
  - 通过复制url下载
![image](/images/us3Browser/downloadurl.jpg)
  - 下载并保存到本地
![image](/images/us3Browser/download1.jpg)
![image](/images/us3Browser/download2.jpg)
* 批量下载，可以一次选中多个文件或者文件夹下载并保存到本，选中想要下载的文件，单击下载按钮，弹出对话框，选择存储本地路径，确定即可
![image](/images/us3Browser/batchdownload.png)
![image](/images/us3Browser/batchdownload1.png)
### 更多操作
* 更多操作可以进行批量操作，主要包含删除、修改存储类型和解冻功能，解冻只针对归档存储类型有效
  - 删除
  ![image](/images/us3Browser/delete1.png)
  ![image](/images/us3Browser/delete2.png)
  - 修改存储类型
  ![image](/images/us3Browser/modifystoragetype1.png)
  ![image](/images/us3Browser/modifystoragetype2.png)
  - 解冻
  ![image](/images/us3Browser/thaw1.png)
### 历史记录
* 主要保存了用户操作的任务（删除、修改存储类型以及解冻）、上传以及下载的历史操作记录，包含总数统计以及成功数量统计，页面展示包含存储桶、本地文件路径、目标文件路径以及进度条展示等相关信息。
 ![image](/images/us3Browser/history.png)
  - 任务历史记录，可以通过操作区下面的全部删除按钮清空历史记录，也可以通过单个删除按钮删除该条记录
   ![image](/images/us3Browser/tasks.png)
  - 上传历史记录，操作区包含全部暂停、全部恢复以及全部删除功能，上传过程中文件没有结束，可以单个暂停也可以全部暂停，但是小文件不支持暂停，可以通过全部恢复运行按钮恢复上传任务，删除功能同上
   ![image](/images/us3Browser/upload.png)
  - 下载历史记录，操作区功能同上传历史记录的操作区功能相同
   ![image](/images/us3Browser/download.png)
### 文件文件夹重命名
针对bucket下面的文件或者目录需要重命名操作，点击目录或者文件后面功能区的三个点，点击重命名，输入新的名称即可
![image](/images/us3Browser/rename.png)
## 账号管理
* 登录账号
![image](/images/us3Browser/accountlogin.png)
* 修改信息,可以修改当前账号的登录信息
![image](/images/us3Browser/modifyaccount.png)
![image](/images/us3Browser/modifyaccount2.png)
* 退出账号，退出当前登录的账号
![image](/images/us3Browser/quitaccount.png)



## 参数设置
设置操作主要包含上传下载、列表页以及其他设置，上传下载设置主要包含并发数、失败重试次数以及大文件的分片大小，列表页设置主要可以设置主页面需要展示的内容，其他设置主要是可以设置默认下载保存路径
![image](/images/us3Browser/setup.png)


