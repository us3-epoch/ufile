# 下载与环境准备

## 下载地址

Ⅰ.Windows

- [us3cli-win32.exe](http://ufile-release.cn-bj.ufileos.com/us3cli%2Fus3cli-win32.exe)
- [us3cli-win64.exe](http://ufile-release.cn-bj.ufileos.com/us3cli%2Fus3cli-win64.exe)

Ⅱ.Linux

​x86架构

- [us3cli-linux32](http://ufile-release.cn-bj.ufileos.com/us3cli%2Fus3cli-linux32)
- [us3cli-linux64](http://ufile-release.cn-bj.ufileos.com/us3cli%2Fus3cli-linux64)

arm架构

- [us3cli-arm32](http://ufile-release.cn-bj.ufileos.com/us3cli%2Fus3cli-arm32)
- [us3cli-arm64](http://ufile-release.cn-bj.ufileos.com/us3cli%2Fus3cli-arm64)

Ⅲ.Mac

- [us3cli-mac](http://ufile-release.cn-bj.ufileos.com/us3cli%2Fus3cli-mac)

## 环境准备

### Linux环境（Linux 64位版本）

1.下载

```
wget http://ufile-release.cn-bj.ufileos.com/us3cli/us3cli-linux64
```

2.添加可执行权限

```
chmod +x us3cli-linux64 
```

3.开始创建配置

```
./us3cli-linux64 config
```

### Windows环境（Windows 64位版本）

以下命令以Windows 64为例。

1.[点击下载](http://ufile-release.cn-bj.ufileos.com/us3cli/us3cli-win64.exe)

2.以管理员身份打开cmd面板，切换到us3cli-win64.exe文件所在路径。

3.执行config命令创建配置。

```
us3cli-win64 config
```

注意事项：

1.请使用管理员身份打开cmd命令行。

2.下载后请不要直接点击执行us3cli-win64.exe文件，该工具需要使用命令行工具打开执行。

3.如果点击下载无反应，请右键单击复制链接地址，输入到浏览器地址输入框打开。

### Mac环境

1.使用curl下载。

```
curl -o us3cli-mac http://ufile-release.cn-bj.ufileos.com/us3cli/us3cli-mac
```

2.添加可执行权限。

```
chmod +x us3cli-mac
```

3.开始创建配置。

```
./us3cli-mac config
```

