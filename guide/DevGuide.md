# 开发指南

## 基本概念介绍

本部分将向您介绍本产品中涉及的几个基本概念，以便于您更好地理解对象存储UFile 产品。

### 存储空间（Bucket）

存储空间是您用于存储对象（Object）的容器，所有的对象都必须隶属于某个存储空间。您可以设置和修改存储空间属性用来控制地域、访问权限、生命周期等，这些属性设置直接作用于该存储空间内所有对象，因此您可以通过灵活创建不同的存储空间来完成不同的管理功能。

- 同一个存储空间的内部是扁平的，没有文件系统的目录等概念，所有的对象都直接隶属于其对应的存储空间。
- 每个用户可以拥有多个存储空间。
- 存储空间的名称在 UFile 范围内必须是全局唯一的，一旦创建之后无法修改名称。
- 存储空间内部的对象数目没有限制。

存储空间的命名规范如下：

只能包括小写字母、数字和短横线（-）。

- 必须以小写字母或者数字开头和结尾。
- 长度必须在 3–63 字节之间。

### 对象/文件（Object）

对象是 UFile 存储数据的基本单元，也被称为 UFile的文件。对象由元信息（Object Meta），用户数据（Data）和文件名（Key）组成。对象由存储空间内部唯一的Key来标识。对象元信息是一对键值对，表示了对象的一些属性，比如最后修改时间、大小等信息，同时用户也可以在元信息中存储一些自定义的信息。

对象的生命周期是从上传成功到被删除为止。在整个生命周期内，对象内容无法编辑，您可以通过重复上传同名的对象来覆盖之前的对象。

对象的命名规范如下：

- 使用 UTF-8 编码。
- 长度必须在 1–1023 字节之间。
- 不能以反斜线（\\）开头。

说明 对象名称需要区分大小写。如无特殊说明，本文档中的对象、文件称谓等同于Object。

### Region（地域）

Region 表示 UFile的数据中心所在的地域，物理位置。用户可以根据费用、请求来源等综合选择数据存储的Region。一般来说，距离用户更近的 Region 访问速度更快。详情请查看 UFile 已经开通的 Region。

Region是在创建 Bucket 的时候指定的，一旦指定之后就不允许更改。该 Bucket 下所有的 Object 都存储在对应的数据中心，目前不支持 Object 级别的 Region 设置。

### 访问密钥

访问密钥指的是访问身份验证中用到的 public\_key 和 private\_key。UFile 通过使用 public\_key 和 private\_key 对称加密的方法来验证某个请求的发送者身份。public\_key 用于标识用户，private\_key 是用户用于加密签名字符串和 UFile 用来验证签名字符串的密钥，其中 private\_key 必须保密。对于 UFile 来说，访问密钥的来源有：

- Bucket 的拥有者申请的访问密钥。
- 被 Bucket 的拥有者通过 Token 授权给第三方请求者的访问密钥。

更多访问密钥介绍请参见访问控制。

### 强一致性

Object 操作在 UFile 上具有原子性，操作要么成功要么失败，不会存在有中间状态的 Object。UFile 保证用户一旦上传完成之后读到的 Object 是完整的，UFile 不会返回给用户一个部分上传成功的 Object。

Object 操作在 UFile 上同样具有强一致性，用户一旦收到了一个上传（PUT）成功的响应，该上传的 Object 就已经立即可读，并且 Object 的冗余数据已经写成功。不存在一种上传的中间状态，即 read-after-write 却无法读取到数据。对于删除操作也是一样的，用户删除指定的 Object 成功之后，该 Object 立即变为不存在。

### 数据冗余机制

UFile 采用数据冗余存储机制，将每个对象的不同冗余存储在同一个区域内多个设施的多个设备上，确保硬件失效时的数据可靠性和可用性。

- UFile Object 操作具有强一致性，用户一旦收到了上传/复制成功的响应，则该上传的 Object 就已经立即可读，且数据已经冗余写入到多个设备中。
- UFile 会通过计算网络流量包的校验和，验证数据包在客户端和服务端之间传输中是否出错，保证数据完整传输。
- UFile 的冗余存储机制，可支持两个存储设施并发损坏时，仍维持数据不丢失。
- 当数据存入 UFile 后，UFile 会检测和修复丢失的冗余，确保数据可靠性和可用性。
- UFile 会周期性地通过校验等方式验证数据的完整性，及时发现因硬件失效等原因造成的数据损坏。当检测到数据有部分损坏或丢失时，UFile 会利用冗余的数据，进行重建并修复损坏数据。

### UFile 与文件系统的对比

| 对比项     | UFile   | 文件系统 |
| ---------- | ------- | -------- |
| 数据模型   | UFile 是一个分布式的对象存储服务，提供的是一个 Key-Value 对形式的对象存储服务。| 文件系统是一种典型的树状索引结构。|
| 数据获取   | 根据 Object 的名称（Key）唯一的获取该 Object 的内容。| 一个名为 test1/test.jpg 的文件，访问过程需要先访问到 test1 这个目录，然后再在该目录下查找名为 test.jpg 的文件。<br> 虽然用户可以使用类似 test1/test.jpg 的名字，但是这并不表示用户的 Object 是保存在test1 目录下面的。对于 UFile 来说，test1/test.jpg 仅仅只是一个字符串，和a.jpg 这种并没有本质的区别。因此不同名称的 Object 之间的访问消耗的资源是类似的。|
| 优势       | 支持海量的用户并发访问。| 支持文件的修改，比如修改指定偏移位置的内容、截断文件尾部等。也支持文件夹的操作，比如重命名目录、删除目录、移动目录等非常容易。|
| 劣势       | UFile 保存的 Object 不支持修改（追加写 Object 需要调用特定的接口，生成的 Object 也和正常上传的 Object 类型上有差别）。用户哪怕是仅仅需要修改一个字节也需要重新上传整个 Object。| 受限于单个设备的性能。访问越深的目录消耗的资源也越大，操作拥有很多文件的目录也会非常慢。|

> UFile 可以通过一些操作来模拟类似文件夹的功能，但是代价非常昂贵。比如重命名目录，希望将 test1 目录重命名成 test2，那么 UFile 的实际操作是将所有以 test1/ 开头的 Object 都重新复制成以 test2/ 开头的 Object，这是一个非常消耗资源的操作。因此在使用 UFile 的时候要尽量避免类似的操作。   

因此，将 UFile
映射为文件系统是非常低效的，也是不建议的做法。如果一定要挂载成文件系统的话，建议尽量只做写新文件、删除文件、读取文件这几种操作。使用
UFile
应该充分发挥其优点，即海量数据处理能力，优先用来存储海量的非结构化数据，比如图片、视频、文档等。

以下是 UFile 与文件系统的概念对比：

| 对象存储 UFile | 文件系统 |
| -------------- | -------------- |
| Object         | 文件 |
| Bucket         | 主目录 |
| Region         | 无 |
| Endpoint       | 无 |
| 访问密钥       | 无 |
| 无             | 多级目录 |
| GetFileList    | 获取文件列表 |
| PutFile        | 写文件 |
| GetFile        | 读文件 |
| DeleteFile     | 删除文件 |
| 无             | 修改文件内容 |
| 无             | 复制文件 |
| 无             | 重命名文件 |

### UFile 术语表

| 英文                      | 中文 |
| ------------------------- | ------------------------------------------------- |
| Bucket                    |  存储空间 |
| Object                    |  对象或者文件 |
| Endpoint                  |  UFile 访问域名 |
| Region                    |  地域或者数据中心 |
| AccessKey                 |  AccessKeyId 和 AccessKeySecret 的统称，访问密钥 |
| Put File                  |  简单上传 |
| Post File                 |  表单上传 |
| Multipart Upload          |  分片上传 |
| Get File                  |  简单下载 |
| Callback                  |  回调 |
| Object Meta               |  文件元信息。用来描述文件信息，例如长度，类型等 |
| Data                      |  文件数据 |
| Key                       |  文件名 |
| ACL (Access Control List) |  存储空间或者文件的权限 |

## 访问域名和数据中心

Region表示UFile的数据中心所在的地域，Endpoint表示UFile对外服务的访问域名。本文主要介绍Region与Endpoint的对应关系。

### UFile开通Region和Endpoint对照表

经典网络情况下各地域Endpoint的内外网设置如下：

| Region中文名称   | Region英文表示   | 外网Endpoint               | 外网支持HTTPS   | 内网支持HTTPS   | UHost访问的内网Endpoint |
| ---------------- | ---------------- | -------------------------- | --------------- | --------------- | ------------------------------------- |
| 华东 1           | cn-sh2           | cn-sh2.ufileos.com         | 是              | 是              | internal-cn-sh2-01.ufileos.com |
| 北京             | cn-bj            | cn-bj.ufileos.com          | 是              | 是              | ufile.cn-north-02.ucloud.cn |
| 华南 1           | cn-gd            | cn-gd.ufileos.com          | 是              | 是              | internal-cn-gd-02.ufileos.com |
| 香港             | hk               | hk.ufileos.com             | 是              | 是              | internal-hk-01.ufileos.com |
| 台北             | tw-tp            | tw-tp.ufileos.com          | 是              | 是              | internal-tw-tp.ufileos.com |
| 洛杉矶           | us-ca            | us-ca.ufileos.com          | 是              | 是              | internal-us-ca-01.ufileos.com |
| 新加坡           | sg               | sg.ufileos.com             | 是              | 是              | internal-sg-01.ufileos.com |
| 雅加达           | idn-jakarta      | idn-jakarta.ufileos.com    | 是              | 是              | internal-idn-jakarta-01.ufileos.com |
| 尼日利亚         | afr-nigeria      | afr-nigeria.ufileos.com    | 是              | 是              | internal-afr-nigeria.ufileos.com |
| 巴西             | bra-saopaulo     | bra-saopaulo.ufileos.com   | 是              | 是              | internal-bra-saopaulo.ufileos.com |
| 孟买             | ind-mumbai       | ind-mumbai.ufileos.com     | 是              | 是              | internal-ind-mumbai.ufileos.com |
| 越南             |                  | vn-sng.ufileos.com         | 是              | 是              | internal-vn-sng.ufileos.com |
| 迪拜             | uae-dubai        | uae-dubai.ufileos.com      | 是              | 是              | internal-uae-dubai.ufileos.com |

说明

- 在分享链接或者做自定义域名绑定（CNAME）的时候建议使用三级域名，即Bucket + Endpoint的形式。以华东2地域内名为ufile-sample的Bucket为例，三级域名为ufile-sample.cn-sh2.ufileos.com。
- 使用SDK时，请将 http:// 或 https:// + Endpoint 作为初始化的参数。以华东1的Endpoint为例，建议将初始化参数设置为http://cn-sh2.ufileos.com 或者 [https://cn-sh2.ufileos.com，不建议将三级域名（即http://bucket.cn-sh2.ufileos.com）作为初始化参数。](https://oss-cn-shanghai.aliyuncs.com，不建议将三级域名（即http:/bucket.oss-cn-shanghai.aliyuncs.com）作为初始化参数。)
- 原地址ufile.ucloud.cn 默认指向华北1 地域外网地址。

### UFile访问域名使用规则

UFile会为每一个存储空间（Bucket）分配默认的访问域名，本文介绍UFile访问域名的构成规则及使用方式。

### UFile域名构成规则

针对UFile的网络请求，除了CreateBucket这个API以外，其他所有请求的域名都是带有指定Bucket信息的三级域名组成的。

访问域名结构：BucketName.Endpoint。BucketName为您的存储空间名称，Endpoint为存储空间对应的地域域名。

说明

- UFile以HTTP RESTful API的形式对外提供服务，当访问不同的地域（Region）时，需要不同的访问域名。

- Endpoint分内网和外网访问域名。例如，华东1（上海）地域的外网Endpoint是cn-sh2.ufileos.com，内网Endpoint是internal-cn-sh2-01.ufileos.com。Region和Endpoint对照表请参考[访问域名和数据中心](https://help.aliyun.com/document_detail/31837.html#concept-zt4-cvy-5db)。

- 您也可以通过绑定自定义域名或绑定CDN加速域名，将UFile的外网访问域名替换为您的自有域名。

### 通过外网访问UFile服务

外网指的是互联网。通过外网访问产生的流入流量（写）是免费的，流出流量（读）是收费的。

说明 UFile费用详情请参见[*UFile服务价格页*](#_产品定价)和[*计量项和计费项*](#_计费案例)。

外网访问UFile有如下两种方式：

- 访问方式一：访问时以URL的形式来表示UFile的资源。UFile的URL构成如下：

`&lt;Schema&gt;://&lt;Bucket&gt;.&lt;外网Endpoint&gt;/&lt;Object&gt;`

- Schema：HTTP或者为HTTPS

- Bucket：UFile存储空间

- Endpoint：Bucket所在数据中心的访问域名，您需要填写外网Endpoint

- Object：上传到UFile上的文件

示例：如您的Region为华东1（cn-sh2），Bucket名称为abc，Object访问路径为myfile/aaa.txt，那么您的外网访问地址为

`abc.cn-sh2.ufileos.com/myfile/aaa.txt`

> 注意 UFile访问域名需携带Object访问路径才可以被访问，仅访问域名，如 abc.cn-sh2.ufileos.com，会有报错提示。若您希望直接访问UFile访问域名，可以通过配置[静态网站托管](https://help.aliyun.com/document_detail/31939.html#concept-trc-jn2-vdb)来实现。

您还可以直接将Object的URL放入HTML中使用，如下所示：
```html
<img src="https://abc.cn-sh2.ufileos.com/myfile/aaa.jpg" />;
```

- 访问方式二： 通过UFile SDK配置外网访问域名。

UFile
SDK会对您的每一个操作拼接访问域名。但您在对不同地域的Bucket进行操作的时候需要设置不同的Endpoint和Region。

以Java
SDK为例，对华东1的Bucket进行操作时，需要在对类实例化时设置Endpoint和Region.

```java
ObjectAuthorization OBJECT_AUTHORIZER = new UfileObjectLocalAuthorization("Your PublicKey", "Your PrivateKey");

// 对象操作需要ObjectConfig来配置您的地区和域名后缀
ObjectConfig config = new ObjectConfig("cn-sh2", "ufileos.com");

// 同步方式
UfileClient.object(OBJECT_AUTHORIZER, config).APIs.execute();

// 异步方式
UfileClient.object(OBJECT_AUTHORIZER, config).APIs.executeAsync(UfileCallback<T>);
```

通过内网访问UFile服务

内网指的是UCloud产品之间的内网通信网络，例如您通过UHost云服务器访问UFile服务。内网产生的流入和流出流量、请求次数均免费。

说明 UFile费用详情请参见[*UFile服务价格页*](#_产品定价)和[*计量项和计费项*](#_计费案例)。

### 通过内网访问UFile服务

内网指的是UCloud产品之间的内网通信网络，例如您通过UHost云服务器访问UFile服务。内网产生的流入和流出流量均免费。

说明 UFile费用详情请参见[*UFile服务价格页*](#_产品定价)和[*计量项和计费项*](#_计费案例)。

内网访问UFile有如下两种方式：

- 访问方式一：访问时以URL的形式来表示UFile的资源。UFile的URL构成如下：

```
<Schema>://<Bucket>.<外网Endpoint>/<Object>;
```

- Schema：HTTP或者为HTTPS

- Bucket：UFile存储空间

- Endpoint：Bucket所在数据中心的访问域名，您需要填写外网Endpoint

- Object：上传到UFile上的文件

示例：如您的Region为华东1（cn-sh2），Bucket名称为abc，Object访问路径为myfile/aaa.txt，那么您的外网访问地址为：

```bash
http://abc.internal-cn-sh2-01.ufileos.com/myfile/aaa.txt
```

- 访问方式二：通过UHost使用UFile SDK配置内网访问域名。

以Java SDK为例，对华东1地域的Bucket进行操作时，需要在对类实例化时设置Endpoint：

```java
ObjectAuthorization OBJECT_AUTHORIZER = new UfileObjectLocalAuthorization("Your PublicKey", "Your PrivateKey");

// 对象操作需要ObjectConfig来配置您的地区和域名后缀
ObjectConfig config = new ObjectConfig("internal-cn-sh2-01", "ufileos.com");

// 同步方式
UfileClient.object(OBJECT_AUTHORIZER, config).APIs.execute();

// 异步方式
UfileClient.object(OBJECT_AUTHORIZER, config).APIs.executeAsync(UfileCallback<T>);
```

## 存储空间

### 创建存储空间

在上传文件（Object）到 UFile 之前，您需要使用 UFile API 中的 CreateBucket 接口来创建一个用于存储文件的存储空间（Bucket），存储空间具有各种配置属性，包括地域、访问权限以及其他元数据。或者使用[*UFile控制台*](https://console.ucloud.cn/ufile/ufile)来创建一个存储空间（Bucket），并设置存储空间的访问权限。

> 说明：创建存储空间的 API 接口的详细信息请参见 [CreateBucket](https://help.aliyun.com/document_detail/31959.html#reference-wdh-fj5-tdb)。

使用限制

- 同一用户创建的存储空间总数不能超过 100 个。

- 每个存储空间的名字全局唯一，否则会创建失败。

- 存储空间的名称需要符合命名规范。

- 存储空间一旦创建成功，名称和所处地域不能修改。

### 权限控制

您可以在创建存储空间是或者是创建后设置设置相应的存储空间权限（ACL）。更多信息，请参见[*设置存储空间读写权限*](#_权限控制_1)。

### 更多参考

- *简单上传*

- *简单下载*

- *删除存储空间*

- *权限控制概述*

### 设置空间读写权限

您可以在创建存储空间（Bucket）时设置存储空间的访问权限（ACL），也可以在创建Bucket后根据自己的业务需求修改存储空间的ACL，该操作只有存储空间的拥有者可以执行。

存储空间有两种访问权限：

| 权限值    | 中文名称         | 权限对访问者的限制 |
| --------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------ |
| public    | 公共读，私有写   | 只有该存储空间的拥有者可以对该存储空间内的文件进行写操作，任何人（包括匿名访问者）都可以对该存储空间中的文件进行读操作。 |
|           |                  | `警告 互联网上任何用户都可以对该 Bucket 内文件进行访问，这有可能造成您数据的外泄以及费用激增，请谨慎操作。` |
| private   | 私有读写         | 只有该存储空间的拥有者可以对该存储空间内的文件进行读写操作，其他人无法访问该存储空间内的文件。|

更多参考

- *权限控制概述*

### 获取空间地域信息

您可以通过UFile API的DescribeBucket接口获取存储空间（Bucket）所属的地域，即数据中心的物理位置信息。

> 说明 获取存储空间地域信息的API详情请参考[*DescribeBucket*](#_7.6.2获取Bucket信息-DescribeBucket)。

您可以通过返回的JSON结果中的DataSet对象中的Region字段查看存储空间所在的地域信息，例如：华北1（北京）的Region字段信息显示为cn-bj2。请参见 UFile 提供的[*访问域名*](#Region（地域）)。

### 查看空间列表

存储空间创建之后，您可以通过UFile API的DescribeBucket接口获取存储空间列表信息。

> 说明 调用DescribeBucket接口时把BucketName参数为空即为获取账户下的存储空间列表，API接口的详细信息请参见[*DescribeBucket*](#_7.6.2获取Bucket信息-DescribeBucket)。

### 绑定自定义域名

您的文件上传到UCloud UFile 的 Bucket 后，会自动生成该文件的访问地址，您可以使用此地址访问 Bucket 内的文件。若您希望通过自定义域名访问这些文件，需要将自定义域名绑定到文件所在的存储空间，并添加 CNAME 记录指向存储空间对应的外网域名。

> 注意 按照中华人民共和国《互联网管理条例》的要求，所有需要绑定自定义域名的用户，必须提前将域名在工信部备案。若您的域名未备案，您可通过UCloud云提供的备案服务进行备案。

| 操作方式   | 说明 |
| ---------- | ------------------------ |
| 控制台     | Web 应用程序，直观易用 |

绑定自定义域名，您需要了解以下概念：

- 用户域名、自定义域名、自有域名：您在域名服务商处购买的域名。

- UFile 访问域名或 Bucket 域名：UFile 为您的 Bucket 分配的的访问域名。您可以使用此域名访问您 Bucket 里的资源。如果您想使用您自己的用户域名访问 UFile Bucket，必须将您的用户域名绑定到 UFile 域名，即在云解析中添加CNAME记录。

- UCloud CDN 域名：UCloud CDN 为您的用户域名分配的 CDN 加速域名。将您的用户域名绑定到 CDN 加速域名，即在云解析中添加 CNAME 记录，才可以加速访问 UFile Bucket 里的资源。

绑定自定义域名应用场景

例如，用户 A 拥有一个域名为 img.abc.com 的网站，网站的一个图片链接为http://img.abc.com/logo.jpg。为方便后续管理，用户 A 想要访问网站中图片的请求转到 UFile，并且不想修改任何网页的代码，也就是对外链接不变。绑定自定义域名可以满足这个需求。流程如下：

1. 在 UFile 上创建一个名为 abc-img 的 Bucket，并将其网站上的图片上传至 Bucket。

2. 通过 UFile 控制台，将 img.abc.com 这个自定义的域名绑定在 abc-img。

3. 绑定成功之后，UFile 后台会将 img.abc.com 做一个映射到 abc-img。

4. 在自己的域名服务器上，添加一条 CNAME 规则，将 img.abc.com 映射成 abc-img.cn-bj.ufileos.com（即 abc-img 的 UFile 域名）。

5. http://img.abc.com/logo.jpg 请求到达 UFile 后，UFile 通过 img.abc.com 和 abc-img 的映射关系，将访问转到 abc-img 这个 Bucket。也就是说，对http://img.abc.com/logo.jpg的访问，实际上访问的是http://abc-img.cn-bj.ufileos.com/logo.jpg。

绑定自定义域名前后流程对比如下：

|            | 绑定自定义域名前                         | 绑定自定义域名后 |
| ---------- | ---------------------------------------- | -------------------------------------------- |
| 流程对比   | 1.  访问 http://img.abc.com/logo.jpg     | 1.  访问 http://img.abc.com/logo.jpg |
|            | 2.  DNS 解析到用户服务器 IP。            | 2.  DNS 解析到 abc-img.cn-bj.ufileos.com。|
|            | 3.  访问用户服务器上的logo.jpg。         | 3.  访问 UFile 上 abc-img 里的 logo.jpg。|

### 管理跨区域复制

### 设置跨域

### 删除存储空间

您可以通过 UFile API 的 DeleteBucket 接口删除您创建的存储空间。

> 说明 删除存储空间的 API 详细信息可参考[DeleteBucket](https://docs.ucloud.cn/api/ufile-api/delete_bucket)

> 如果存储空间不为空（存储空间中有文件或者是尚未完成的分片上传），则存储空间无法删除，必须删除存储空间中的所有文件和未完成的分片文件后，存储空间才能成功删除。如果想删除存储空间内部所有的文件，推荐使用生命周期管理。

## 上传文件

### 简单上传

简单上传指的是使用UFile API中的PutObject方法上传单个文件（Object）。简单上传适用于一次HTTP请求交互即可完成上传的场景，比如小文件（小于1GB）的上传。

说明

- 简单上传的API接口的详细信息请参见PutFile。

- 大文件（大于5GB）的上传请使用分片上传。

- 大小限制：Object的大小不能超过5GB。

- 命名限制：

- 使用UTF-8编码。

- 长度必须在1–1023字节之间。

- 不能以正斜线（/）或者反斜线（\\）字符开头。

- 为了防止第三方未经授权往您的Bucket里上传数据，UFile提供了Bucket和Object级别的访问权限控制。

- 在文件上传到UFile上后，您可以通过上传回调来向指定的应用服务器发起回调请求，进行下一步操作。

- 如果上传的是图片，您还可以进行图片处理。

### 表单上传

表单上传是指使用UFile API中的PostFile请求来完成Object的上传，上传的Object不能超过5GB。

> 说明 表单上传的API接口详细信息请参见PostFile。

适用场景

表单上传非常适合嵌入在HTML网页中来上传Object，比较常见的场景是网站应用，以招聘网站为例：

|            | 不使用表单上传                    | 表单上传 |
| ---------- | --------------------------------- | -------------------------- |
| 流程对比   | 网站用户上传简历。                | 网站用户上传简历。|
|            | 网站服务器回应上传页面。          | 网站服务器回应上传页面。 |
|            | 简历被上传到网站服务器。          | 简历上传到UFile。|
|            | 网站服务器再将简历上传到UFile。   | |

- 从流程上来说，使用表单上传，少了一步转发流程，更加方便。

- 从架构上来说，原来的上传都统一走网站服务器，上传量过大时，需要扩容网站服务器。采用表单上传后，直接从客户端上传数据到UFile，上传量过大时，压力都在UFile上，由UFile来保障服务质量。

SDK demo

请参见[*Java SDK*](#Java SDK 快速入门)。


- 大小限制：使用表单上传时，Object不能超过5GB。

- 命名限制：

- 使用UTF-8编码。

- 长度必须在1–1023字节之间。

- 不能以正斜线（/）或者反斜线（\\）字符开头。

- 为了防止第三方未经授权往您的Bucket里上传数据，UFile提供了Bucket和Object级别的访问权限控制。

### 分片上传和断点续传

UCloud UFile提供的分片上传（Multipart Upload）和断点续传功能，可以将要上传的文件分成多个数据块（UFile里又称之为Part）来分别上传，上传完成之后再调用UFile的接口将这些Part组合成一个Object来达到断点续传的效果。

1. 适用场景

当使用简单上传（PutFile）功能来上传较大的文件到UFile的时候，如果上传的过程中出现了网络错误，那么此次上传失败，重试必须从文件起始位置上传。针对这种情况，您可以使用分片上传来达到断点续传的效果。

相对于其他的上传方式，分片上传适用于以下场景：

- 恶劣的网络环境：如手机端，当出现上传失败的时候，可以对失败的Part进行独立的重试，而不需要重新上传其他的Part。

- 断点续传：中途暂停之后，可以从上次上传完成的Part的位置继续上传。

- 加速上传：要上传到UFile的本地文件很大的时候，可以并行上传多个Part以加快上传。

- 流式上传：可以在需要上传的文件大小还不确定的情况下开始上传。这种场景在视频监控等行业应用中比较常见。

2. 断点续传操作方式

在使用分片上传的过程中，如果因各种意外上传中断，可以再次对该文件执行分片上传，本地操作系统会保存上次分片上传的错误的位置，会重新上传未上传成功的片，从而达到断点续传的效果。暂停和恢复上传实现原理也是一样的。

- 大小限制：在这种上传方式下，Object的大小是由Part来决定的。每块Part为固定的4MB（最后一块可以比4MB小）。Object的大小不能超过48.8TB。

- 命名限制

- 使用UTF-8编码。

- 长度必须在1–1023字节之间。

- 不能以正斜线（/）或者反斜线（\\）字符开头。

3. 分片上传流程

- 将要上传的文件按照一定的大小分片。

- 初始化一个分片上传任务（[InitiateMultipartUpload](https://docs.ucloud.cn/api/ufile-api/initiate_multipart_upload）。

- 逐个或并行上传分片（[UploadPart](https://docs.ucloud.cn/api/ufile-api/upload_part）。

- 完成上传（[FinishMultipartUpload](https://docs.ucloud.cn/api/ufile-api/finish_multipart_upload）。

该过程需注意以下几点：

> 除了最后一块Part，其他Part的大小不能小于4MB，否则会导致调用[FinishMultipartUpload](https://docs.ucloud.cn/api/ufile-api/finish_multipart_upload)接口时失败。

> 要上传的文件切分成Part之后，文件顺序是通过上传过程中指定的partNumber来确定的，实际执行中并没有顺序要求，因此可以实现并发上传。

> 具体的并发个数并不是越多速度越快，要结合用户自身的网络情况和设备负载综合考虑。

> 默认情况下，已经上传但还没有调用[FinishMultipartUpload](https://docs.ucloud.cn/api/ufile-api/finish_multipart_upload)的Part是不会自动回收的，因此如果要终止上传并删除占用的空间请调用AbortMultipartUpload。

4. 上传的安全及授权

为了防止第三方未经授权往您的Bucket里上传数据，UFile提供了Bucket和Object级别的访问权限控制。

5. 上传后续操作

- 在文件上传到UFile上后，您可以通过上传回调来向指定的应用服务器发起回调请求，进行下一步操作。

- 如果上传的是图片，您还可以进行[图片处理]()。

### 上传回调

UCloud UFile在上传文件完成的时候可以提供回调（Callback）给应用服务器。您只需要在发送给UFile的请求中携带相应的 Callback 参数，即能实现回调。

1. 上传回调策略

该回调策略指定在上传文件完成后(携带可能的参数)去请求一个用户指定的服务地址(目前仅支持http,并且仅支持请求一个指定服务)。在得到用户服务器的回应(必须是application/json格式)后将用户服务器的返回值透传给用户。回调服务的地址使用json格式进行封装，格式如下：

```javascript
{
   "callbackUrl" : "http://test.ucloud.cn", //指定回调服务的地址

   "callbackBody" : "key1=value1&key2=value2" //传递给回调服务的参数
}
```

携带上传策略的上传请求和不带上传策略的上传请求之间的区别是在授权字段Authorization部分,API其他格式均保持不变。

不需要上传策略时，我们在上传时的Authorization格式为:

```
Authorization: UCloud *publickey*:*signature*
```

如需使用上传策略,则格式变为:

```
Authorization: UCloud *publickey*:*signature*:*encodedPutPolicy*
```

其中
```
encodedPutPolicy = base64(json_encode(put_policy))
```

> 注意json格式请使用压缩后的格式,不要携带空白字符,除非key/value本身是含有空白字符的字符串。这里的base64是URLSafe的base64

此外,旧有方式的签名字符串计算方式是：

```
signstring = HTTP-Verb + "\\n" +

Content-MD5 + "\\n" +

Content-Type + "\\n" +

Date + "\\n" +

CanonicalizedUCloudHeaders +

CanonicalizedResource
```

当上传请求需要执行上传策略的时候,签名字符串其他部分不变,需要在末尾追加上传策略的base64字符串,即

```
signstring_with_putpolicy = signstring + base64(json_encode(put_policy))
```

#### 用法示例

假设上传的文件为：flower.jpg

上传策略为：

```javascript
{
   "callbackUrl" : " http://inner.umedia.ucloud.com.cn/CreateUmediaTask",

   "callbackBody" : "url=http://demo.ufile.ucloud.cn/test.mp4&patten_name=mypolicy"
}
```

则没有上传策略的上传请求是:

```
PUT /flower.jpg HTTP/1.1

Content-Length: 123456

Content-Type: image/jpeg

Host: test.ufile.ucloud.cn

Authorization: UCloud aGVsbHdvZGhhZGhhc2RoYWRzZGFkaHNkaGFkaGhkaGxrc2Rh:bTgzdWhkZGlsYS9kLmFkYWRhc2Ruaw==
```

而携带了上传策略的上传请求是:

```
PUT /flower.jpg HTTP/1.1

Content-Length: 123456

Content-Type: image/jpeg

Host: test.ufile.ucloud.cn

Authorization: UCloud aGVsbHdvZGhhZGhhc2RoYWRzZGFkaHNkaGFkaGhkaGxrc2Rh:ZGFkLHBwMz0xZGthZGFkYXNkYQ==:XCJjYWxsYmFja1VybFwiOlwiIGh0dHA6Ly9pbm5lci51bWVkaWEudWNsb3VkLmNvbS5jbi9DcmVhdGVVbWVkaWFUYXNrXCIsXCJjYWxsYmFja0JvZHlcIjpcInVybD1odHRwOi8vZGVtby51ZmlsZS51Y2xvdWQuY24vdGVzdC5tcDQmIHBhdHRlbl9uYW1lPW15cG9saWN5XCI=
```

> 注：签名与bucket相关，示例中签名仅作参考。

## 下载文件

### 简单下载

简单下载是通过UFile API的GetObject接口，下载已经上传的文件（Object），Object下载是使用HTTP的GET请求来完成的。

1. 说明

- 简单下载的API接口详细信息请参见[*GetFile*](#_4.5.1简单下载)。

- Object的URL生成规则请参考UFile的访问。

- 如果需要使用自定义域名来访问Object，请参考自定义域名访问UFile。

2. 下载的安全及授权

> 为了防止第三方未经授权从您的Bucket里下载数据，UFile提供了Bucket和Object级别的访问权限控制。详情请参见[*权限控制*](#_权限控制_1)。

3. 更多参考

如果需要实现断点下载请参考断点续传下载。

### 断点续传下载

UFile提供了从Object指定的位置开始下载的功能，在下载大的Object的时候，可以分多次下载。如果下载中断，重启的时候也可以从上次完成的位置开始继续下载。

和简单上传类似，您也需要对该Object有读权限。通过设置参数Range来支持断点续传，对于比较大的Object建议使用该功能。Range的定义可参考HTTP RFC。如果在请求头中使用Range参数，则返回消息中会包含整个文件的长度和此次返回的范围。例如：Content-Range: bytes 0–9/44，表示整个文件长度为44，此次返回的范围为0–9。如果不在范围内，则传送整个文件，并且不在结果中提及Content-Range，返回码为206。

#### 下载的安全及授权

> 为了防止第三方未经授权从您的Bucket里下载数据，UFile提供了Bucket和Object级别的访问权限控制。详情请参见[*权限控制*](#_权限控制_1)。

## 管理文件

### 管理文件元信息

文件元信息（Object Meta）是对上传到UFile的文件的属性描述。文件元信息可以在各种方式上传时进行设置。

| 名称             | 描述 |
| ---------------- | -------------------- |
| Content-Length   | 该 Object 大小 |
| Content-Type     | 该 Object 文件类型 |

更多参考

您也可以在以下操作中添加文件元信息：

- *简单上传*

- *分片上传和断点续传*

### 查看文件列表

您可以通过 UFile API 中的 PrefixFileList 接口列出您在存储空间（Bucket）中上传的文件（Object）。

> 说明 查看文件列表的 API 详细信息请参考 [*PrefixFileList*](#_7.7.4 前缀列表查询 - PrefixFileList)。

您可以调用通过 PrefixFileList 接口，一次性得到某一 Bucket 下最多 1000 个 Object 的列表。通过下面的三个参数，您可以实现多种列举功能：

| 名称     | 作用 |
| -------- | ---------------------------------------------------------------------------------------------------------------------- |
| prefix   | 限定返回的Object key必须以Prefix作为前缀。注意使用Prefix查询时，返回的key中仍会包含Prefix。utf-8编码，默认为空字符串 |
| marker   | 设定结果从Marker之后按字母排序的第一个开始返回。标志字符串，utf-8编码，默认为空字符串 |
| limit    | 文件列表数目，默认为100 |

### 删除文件

删除文件即删除上传到存储空间（Bucket）中的文件（Object）。

UFile允许您执行如下删除动作：

-   单个删除：指定某个Object进行删除。

-   自动删除：如果需要删除的Object数目很多，而且删除的Object有一定的规律，比如定期删除某些天之前的Object，或者是要清空整个Bucket，推荐使用生命周期管理来自动删除Object。设置了生命周期规则之后，UFile会根据规则自动删除到期的Object，从而极大减少您发送删除请求的次数，提高删除效率。

### 管理回源设置

通过回源设置，对于获取数据的请求以多种方式进行回源读取，满足您对于数据热迁移、特定请求重定向等需求。

对UFile的每条GET请求的URL按设定的规则进行匹配，然后按照设定的规则进行回源。最多配置5条规则，顺序匹配，直到匹配到有效规则。目前回源类型为镜像方式。

### 镜像方式

如果配置了镜像回写，则对一个不存在的文件进行GET操作时，会向源地址请求这个文件，返回给用户，并同时写入到UFile。

1. 使用场景

镜像回源主要用于无缝迁移数据到UFile，即服务已经在自己建立的源站或者在其他云产品上运行，需要迁移到UFile上，但是又不能停止服务，此时可利用镜像回写功能实现。具体场景分析如下：

- 源站有一批冷数据，同时在不断的生成新的热数据。

> 可以先通过迁移工具将冷数据迁移到UFile上，迁移工具为UFile Import
> Tool，同时配置镜像回写，将源站的地址配置到UFile上。当将域名切换到UFile上（或者UCloud的CDN，回源到UFile）之后，就算有一部分新生成的数据没有迁移过来，依然可以在UFile上正常访问到，且访问一次后文件就会存入到UFile。域名切换后，源站已经没有新的数据产生了，此时再扫描一次，将还没有导过来的数据一次性导入到UFile，然后将镜像回写配置删除。
>
> 如果配置的源站是IP地址，那么将域名迁移到UFile后还可以继续镜像到源站，但是如果配置的是一个域名，由于域名本身会解析到UFile或者CDN，那么镜像就失去作用了，在这种情况下，可以另外申请一个域名作为镜像的源站，这个域名与正在服务的域名解析到同一个IP地址，这样服务域名迁移的时候就可以继续镜像到源站了。

- 只切换源站的部分流量到UFile或者CDN，源站本身还在不断的产生数据。

> 迁移方式与上述方式类似，只是流量切换到UFile后，不要将镜像回写配置删掉，这样可以保证切换到UFile或者CDN的流量还是能够取到源站的数据。

##### 细节分析

- 只有当GetFile()本应该返回404的情况下，UFile才会执行镜像回源，向源站请求文件。

- 向源站请求的URL为MirrorURL+object，回写到UFile的文件名为“object”，例如bucket为example-bucket，配置了镜像回写，MirrorURL为 http://www.example-domain.com/ ，文件image/example\_object.jpg不在这个bucket里面，此时去下载这个文件时，UFile将向 http://www.example-domain.com/image/example\_object.jpg 发起GET请求，并将结果同时返回给用户以及写入到UFile，当下载完成后，这个文件就已经存在UFile上了，文件名为image/example\_object.jpg，此时相当于将源站的文件同名的迁移到了UFile上。如果MirrorURL带有path信息，比如 http://www.example-domain.com/dir1/ ，则与上例相同，UFile回源的URL为 http://www.example-domain.com/dir1/image/example\_object.jpg ，写入到UFile的object依然是image/example\_object.jpg，此时相当于将源站的某一个目录下的文件迁移到UFile上。

- 传给UFile的header信息不会传递给源站，querystring信息是否会传递给源站取决于控制台回源规则中的配置。

- 如果源站是chunked编码返回，那么UFile返回给用户的也是chunked编码。

- UFile会将源站的以下头信息返回并存为UFile的头信息：

```
Content-Type

Content-Encoding

Content-DispositionCache-Control

ExpiresContent-LanguageAccess-Control-Allow-Origin
```

- 假设文件已经通过镜像回写到了UFile，如果源站的相同文件发生了变化，那UFile不会更新已经存在于UFile上的文件，因为此时文件已经在UFile上，不符合镜像回写的条件。

- 如果镜像源也不存在此文件，即镜像源返回给UFile的http status为404，那么UFile也返回404给用户，如果是其他非200的状态码（包括因为网络原因等获取不到文件的错误情况），UFile将返回424给用户，错误码为“MirrorFailed”。

## 签名

### UFile请求流程

对UFile的HTTP请求可以根据是否携带身份验证信息分为匿名请求和带身份验证的请求。匿名请求指的是请求中没有携带任何和身份相关的信息；带身份验证的请求指的是按照 UFile API 文档中规定的在请求头部或者在请求 URL 中携带签名的相关信息。

匿名请求流程

- 用户的请求被发送到UFile的HTTP服务器上。

- UFile根据URL解析出Bucket和Object。

- UFile检查Object是否设置了ACL。

- 如果没有设置ACL，那么继续4。

- 如果设置了ACL，则判断Object的ACL是否允许匿名用户访问。

- 允许则跳到5。

- 不允许则拒绝请求，请求结束。

- UFile判断Bucket的ACL是否允许匿名用户访问。

- 允许则继续5。

- 不允许则返回，请求结束。

- 权限验证通过，返回Object的内容给用户。

带身份验证的请求流程

- 用户的请求被发送到UFile的HTTP服务器上。

- UFile根据URL解析出Bucket和Object。

- UFile根据请求的UFile的AccessKeyId获取请求者的相关身份信息，进行身份鉴权。

- 如果未获取成功，则返回，请求结束。

- 如果获取成功，但请求者不被允许访问此资源，则返回，请求结束。

- 如果获取成功，但UFile端根据请求的HTTP参数计算的签名和请求发送的签名字符串不匹配，则返回，请求结束。

- 如果身份鉴权成功，那么继续4。

- UFile检查Object是否设置了ACL。

- 如果Object没有设置ACL，那么继续5。

- 如果Object设置了ACL，UFile判断Object的ACL是否允许此用户访问。

- 允许则跳到6。

- 不允许则拒绝请求，请求结束。

- UFile判断Bucket的ACL是否允许此用户访问。

- 允许则继续6。

- 不允许则返回，请求结束。

- 权限验证通过，返回Object的内容给用户。

身份验证类型

目前访问 UFile 使用的身份验证有两种类型，具体如下：

1. UCloud账号 API密钥


UCloud账号 API密钥特指 Bucket 拥有者的 API 密钥（公私钥），每个UCloud账号提供的 API密钥对拥有的资源有完全的权限。每个UCloud账号仅有一个API 密钥。

> 注意 请避免直接使用UCloud账号的API 密钥。

2. Token 令牌

Token 令牌是UCloud提供的资源访问控制服务。Token 令牌指的是UCloud账号设置的用于访问Bucket资源的授权令牌。通过 Token，您可以集中管理您的用户（比如员工、系统或应用程序），以及控制用户可以访问您名下哪些资源的权限。比如能够限制您的用户只拥有对某一个 Bucket 的读权限。

身份验证具体实现

目前主要有两种身份验证方式：

- API密钥 验证

- Token 令牌验证

当用户以个人身份向 UFile 发送请求时，其身份验证的实现如下：

- 用户将发送的请求按照UFile指定的格式生成签名字符串。

- 用户使用API密钥中的私钥对签名字符串进行加密产生验证码。

- UFile 收到请求以后，通过API密钥中的公钥找到对应的私钥，以同样的方法提取签名字符串和验证码。

- 如果计算出来的验证码和提供的一样即认为该请求是有效的。

- 否则，UFile 将拒绝处理这次请求，并返回 HTTP 403错误。

带身份验证访问UFile的三种方法

- 使用控制台访问UFile：控制台中对用户隐藏了身份验证的细节，使用控制台访问UFile的用户无需关注细节。更多信息请参见[*下载文件*](#_下载文件_1)。

- 使用SDK访问UFile：UFile提供了多种开发语言的SDK，SDK中实现了签名算法，只需要将API密钥中的私钥信息作为参数输入即可。详情请参见各语言SDK文档中的访问控制章节。

- 使用API访问UFile：如果您想用自己喜欢的语言来封装调用RESTful API接口，您需要实现签名算法来计算签名。

### 在Header中包含签名

## 权限控制

UFile的权限控制分两种配置：

1.  对Bucket设置公共访问和私有访问类型；

2.  给Bucket配置Token令牌来管理对Bucket的访问权限；

### 配置存储空间Bucket的类型

存储空间类型分私有空间和公有空间两种：

| 类型     | 解释 |
| -------- | --------------------------------------------------------------------------------------------- |
| 私有空间 | 只有该存储空间的拥有者可以对该存储空间内的文件进行读写操作，其他人无法访问该存储空间内的文件。|
| 公共空间 | 只有该存储空间的拥有者可以对该存储空间内的文件进行写操作，任何人（包括匿名访问者）可以对该存储空间中的文件进行读操作。|

存储空间的类型可以在创建时选择，也可以在创建后进行设置。

在创建后更改空间类型的操作步骤如下：

1. 进入[* UFile 管理控制台*](https://console.ucloud.cn/ufile/ufile)界面。

2. 在左侧存储空间列表中，单击目标存储空间行域名管理右侧的…按钮，在弹出的下来菜单里面选择修改空间类型。

![](images/image15.jpg)

3. 在弹出的修改空间类型窗口里面修改空间类型。

![](images/image16.jpg)

## 令牌管理

UFile令牌功能可以根据用户的需求灵活的开放存储空间和文件管理权限。

一个令牌由一对特殊的公私钥组成。其中包括了：允许操作的存储空间列表、允许操作的文件前缀列表、操作权限和令牌过期时间等属性。

用户可以按需申请不同令牌来完成不同权限的管控。

用户可以通过两种方式管理UFile令牌：

1. 登陆UCloud官方控制台，进入UFile—令牌管理

2. 通过调用[*UFile API*](#_Service操作（令牌、配额）)进行创建、编辑等操作

#### 创建/编辑令牌

![](images/image17.jpg)

1. 令牌名称：用于标识令牌，用户自定义

2. 授权的存储空间：存储空间可选一个或多个，表示该令牌只能操作这些存储空间。一个令牌不能存在多个地区的存储空间，比如只能选择北京或者上海的存储空间，不能同时选择两个地区。

3. 授权的文件前缀：文件前缀可选一个或多个，表示该令牌只能访问这些前缀的文件。

4. 令牌权限：操作权限有上传、下载、删除、文件列表、图片处理，可多选或者不选。

5. 过期时间：令牌过期后会失效。

示例

一个完整的令牌的例子如下所示：

```
公钥：TOKEN_da044c8a-20bc-42a1-8b04-850535c75330

私钥：6318b15a-faf3-4577-890d-79855313dfd9

授权的存储空间：test, test1, test2

授权的文件前缀：file_prefix, file_prefix1, file_prefix2

文件管理权限：上传，下载，删除，文件列表，图片处理

过期时间：2019-04-19 00:00:00
```

