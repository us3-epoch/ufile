# 静态网站托管

## 概念

静态网站是指网站的所有网站资源都由静态内容构成，如HTML、JavaScript、CSS、图片等文件。您可以通过控制台对已经绑定的自定义域名的US3存储桶配置静态网站托管策略

## 使用说明

使用该功能必须先创建的自定义域名。然后根据配置界面进行相应参数设置，其中必填项：

- 默认首页：该首页是您通过自定义域名访问US3存储桶时，返回US3存储桶首页。如果还开通了子目录首页，则子目录下也应该存在该文件，当然您可以根据自己的路径来自定义该文件的内容。
- 默认404页（但开通子项目首页，且文件404规则为NoSuchKey时，默认404不填）是当浏览器访问US3存储桶中的文件不存在(404)时，返回的错误页面。

US3存储桶必须为`公共空间` ，即该存储桶必须是`公共读私有写`。另外通过US3默认的域名进行访问，会将静态网站以文件的形式进行下载。只有通过绑定US3存储桶的自定义域名来进行访问才会在浏览器渲染显示。

详细工作机制，参考举例说明。

## 举例说明

为Bucket开启静态网站托管后，您需要将与默认首页名称相同的文件（例如index.html）上传至目标Bucket，如果Bucket中包含了目录结构prefix/，则目录层级下也必须包含index.html文件。此外，您还需要将与默认404页名称相同的文件（例如error.html）上传至目标Bucket。Bucket的文件结构如下所示：

```
Bucket
├── index.html
├── error.html
├── us3.png
└── prefix/
      └── index.html
```

如果该Bucket绑定了自定义域名example.com，且配置的静态网站默认首页为index.html，默认404页为error.html。则通过自定义域名访问静态网站时，根据是否开通了子目录首页，访问规则如下：

- 未开通子目录首页
  - 当您访问`https://example.com/和https://example.com/prefix/`时，US3会返回`https://example.com/index.html`。
  - 当您访问`https://example.com/us3.png`时，正常获取us3.png文件。
  - 当您访问`https://example.com/helloworld`时，因helloworld不存在，US3会返回`https://example.com/error.html`。

- 已开通子目录首页
  - 当您访问`https://example.com/`时，US3会返回`https://example.com/index.html`。
  - 当您访问`https://example.com/prefix/`时，US3会返回`https://example.com/prefix/index.html`。
  - 当您访问`https://example.com/us3.png`时，正常获取us3.png文件。
  - 当您访问`https://example.com/helloworld`时，因helloworld不存在，US3会根据您设置的文件404规则返回对应信息：
    - 如果文件404规则设置为Redirect（默认值），US3会继续检查helloworld/index.html是否存在。
      - 如果文件存在则返回302，并将访问请求重定向为`https://example.com/helloworld/index.html`。
      - 如果文件不存在则返回404。
    - 如果文件404规则设置为NoSuchKey，则直接返回404。
    - 如果文件404规则设置为Index，US3会继续检查helloworld/index.html是否存在。
      - 如果文件存在则返回200，并直接返回文件内容。
      - 如果文件不存在则返回404。
