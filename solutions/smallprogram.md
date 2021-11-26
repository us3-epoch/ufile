# 在微信小程序端上传文件到US3

### 简介

本文将会介绍并梳理使用小程序原生方法上传文件到US3的流程。

### 预先准备

1. 在控制台上创建一个存储桶，以及具有操作该桶的权限的令牌。
2. 在控制台上获取桶名，终端节点(endpoint)以及令牌的公钥和私钥。

### 上传步骤

接下来我们将以一个图片文件为例，使用PUT 和POST 两种方法来实现文件上传。

1. 首先我们需要拼接好上传文件的url，如果您选择的是PUT方法上传，url通常如下：

   https://<bucket_name>.<endpoint>/<file_name_with_prefix_on_US3>

   而POST方法上传的话，由于文件在US3上的前缀(prefix)和文件名通过POST表单参数来传递，因此我们不需要在url中写入这两个部分：

   https://<bucket_name>.<endpoint>/

2. 上传文件的接口需要我们使用公钥和私钥对http请求的资源计算一个签名，以供服务端查看请求使用的公私钥是否有对应的权限。计算方法请参考 [API签名算法](https://docs.ucloud.cn/ufile/api/authorization?id=%e6%96%87%e4%bb%b6%e7%ae%a1%e7%90%86%e7%ad%be%e5%90%8d%e7%ae%97%e6%b3%95) 。这里提供一个计算签名的js代码demo：

   ```javascript
    const sign = (method, publicKey, privateKey, md5, contentType, date, bucketName, fileName) =>{
    	const CryptoJS = require("crypto-js") //这里使用了crypto-js加密算法库，安装方法会在后面说明
    	const CanonicalizedResource = `/${bucketName}/${fileName}`
     const StringToSign = method + "\n" 
                          + md5 + "\n" 
                          + contentType + "\n" 
                          + date + "\n"
                          + CanonicalizedResource //此处的md5以及date是可选的，contentType对于PUT请求是可选的，对于POST请求则是必须的
     let Signature = CryptoJS.HmacSHA1(StringToSign, privateKey)
     Signature = CryptoJS.enc.Base64.stringify(Signature)
     const Authorization = "UCloud" + " " + publicKey + ":" + Signature
     return Authorization
   }
   ```

   上方demo中的crypto-js是一个常用的javaScript加密算法库，我们建议您使用npm进行安装，具体步骤如下：

   1. 在小程序的开发机上安装 [nodejs](https://nodejs.org/en/) ，完成后尝试在命令行界面运行 `npm` ，以验证安装以及配置是否成功。
   2. 在小程序的跟目录下运行 `npm init` ，来初始化一个npm项目。
   3. 运行 `npm i crypto-js` 命令来安装crypto-js。也可以参考 [npm 的官方页面](https://www.npmjs.com/package/crypto-js)
   4. 最后，使用小程序构建一下npm包，请参考 [构建npm](https://developers.weixin.qq.com/miniprogram/dev/devtools/npm.html#_2-%E6%9E%84%E5%BB%BA-npm)

3. 接下来将给出两个demo以介绍如何使用PUT和POST方法来上传文件：

##### PUT方法

参考文档 [使用PUT方法上传文件](https://docs.ucloud.cn/api/ufile-api/put_file)

```javascript
function uploadByPut(){
  const publicKey = "xxxx-xxxxx-xxxx-xxxxx-xxxxx"
  const privateKey = "xxxxx-xxx-xxxx-xxxx-xxxxx"
  const fileName = "filename"
  const bucketName = "bucketname"
  wx.chooseImage({
     count: 1, // 设置最多1张
     sizeType: ['original', 'compressed'], //所选的图片的尺寸
     sourceType: ['album', 'camera'], //选择图片的来源
     success(res) {
       const image = wx.getFileSystemManager().readFileSync(res.tempFilePaths[0])
       //由于这一代码仅是demo，此处将md5和date两个参数设置为空字符串，在正式的小程序开发中，建议填写这两个参数
       const auth = sign("PUT", publicKey, privateKey, "", "image/jpeg", "", bucketName, fileName)
       wx.request({
         	url: `http://${bucketName}.cn-bj.ufileos.com/${fileName}`, 
         	method: "PUT",
        	header: {
          		'Authorization':auth,
              'content-type': 'image/jpeg'
          },
          data: image,
          success: function (reg) {
            console.log(reg)
          }
        })
       }
    })
  }
```



##### POST方法

此处我们使用小程序原生的wx.uploadFile函数来实现上传。该方法使用的content-type` 为 `multipart/form-data，调用的接口也同样是POST上传的US3接口，请参考 [使用POST表单上传文件](https://docs.ucloud.cn/api/ufile-api/post_file)

```javascript
function uploadByUplodaFile(){
  const publicKey = "xxxx-xxxxx-xxxx-xxxxx-xxxxx"
  const privateKey = "xxxxx-xxx-xxxx-xxxx-xxxxx"
  const fileName = "filename"
  const bucketName = "bucketname"
  wx.chooseImage({
    count: 1, // 设置最多1张
    sizeType: ['original', 'compressed'], //所选的图片的尺寸
    sourceType: ['album', 'camera'], //选择图片的来源
        success (res) {
          const tempFilePaths = res.tempFilePaths
          //注意此处的签名方法为POST, 签名计算所使用的content-type仍然为文件本身的mime-type, 即"image/jpeg"
          const auth = sign("POST", publicKey, privateKey, "", "image/jpeg", "", bucketName, fileName)
          wx.uploadFile({
            url: `https://${bucketName}.cn-bj.ufileos.com/`,
            filePath: tempFilePaths[0],
            name: 'file',
            //此处FormData为表单参数，在此处指定要上传的文件名，以及鉴权签名
            formData: {
              'FileName': fileName,
              'Authorization': auth
            },
            success (res){
              console.log(res)
            }
          })
        }
    })
}
```

那么如果情况不允许使用微信的原生文件上传方法，那么也可以通过拼接multipart/form-data的方式来进行上传。这种方式会麻烦一些，因此如无特殊需求，还是建议使用原生方法进行上传。

```javascript
function uploadByPost(){
  const publicKey = "xxxx-xxxxx-xxxx-xxxxx-xxxxx"
  const privateKey = "xxxxx-xxx-xxxx-xxxx-xxxxx"
  const fileName = "filename"
  const bucketName = "bucketname"
  // boundary可以自定义，这里使用US3官方文档中示例所用的boundary字符串
  const boundary = "----UCloudPOSTFormBoundary"
  // 将string类型数据转换成binary数组
  const strToBinary = (str) => {
    let res = []
    for (var i = 0; i < str.length; i++) {
      res.push(str.charCodeAt(i));
      }
    return res
  }
	wx.chooseImage({
     count: 1, // 设置最多1张
     sizeType: ['original', 'compressed'], //所选的图片的尺寸
     sourceType: ['album', 'camera'], //选择图片的来源
     success(res) {
       //将文件内容读取出来并转换为Uint8Array
       const image = new Uint8Array(wx.getFileSystemManager().readFileSync(res.tempFilePaths[0]))
       //这里的签名规则和原生请求的规则相同
       const auth = sign("POST", publicKey, privateKey, "", "image/jpeg", "", bucketName, fileName)
       //拼接请求体的前半部分，可见和原生请求中的FormData内容相对应。
       //需要将这部分也同样转换成binary数组
       const start =  strToBinary(
                      `--${boundary}` + '\r\n' +
                      'Content-Disposition: form-data; name="FileName"' + '\r\n' +
                      '\r\n' +
                      fileName + '\r\n' +
                      `--${boundary}` + '\r\n'+
                      'Content-Disposition: form-data; name="Authorization"' + '\r\n'+
                      '\r\n' +
                      auth + '\r\n' + 
                      `--${boundary}`+ '\r\n' +
                      'Content-Disposition: form-data; name="file"; filename="MyFilename.jpg"' + '\r\n' +
                      'Content-Type: image/jpeg' + '\r\n' +
                      '\r\n')
       //请求体的结尾，转换为binary数组
       const end = strToBinary('\r\n' +`--${boundary}--`)
       //将前半部分的内容，文件的数据，以及结尾拼接在一起，形成整个请求的binary数组
       const request = start.concat(Array.prototype.slice.call(image), end);
       //将整个reqeust转换成Uint8Array，并通过.buffer参数获取ArrayBuffer对象，wx.request函数最终将请求转化为对应的字符串
       const data = new Uint8Array(totalArray).buffer
       wx.request({
          url: `https://${bucketName}.cn-bj.ufileos.com/`,
           method: "POST",
        header: {
          'content-type':`multipart/form-data; boundary=${boundary}`,
        },
        data: data,
        success: function (reg) {
          console.log(reg)
        }
      })
     }
  })
}
```