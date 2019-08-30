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
- 使用SDK时，请将 http:// 或 https:// + Endpoint 作为初始化的参数。

### UFile访问域名使用规则

UFile会为每一个存储空间（Bucket）分配默认的访问域名，本文介绍UFile访问域名的构成规则及使用方式。

### UFile域名构成规则

针对UFile的网络请求，除了CreateBucket这个API以外，其他所有请求的域名都是带有指定Bucket信息的三级域名组成的。

访问域名结构：BucketName.Endpoint。BucketName为您的存储空间名称，Endpoint为存储空间对应的地域域名。

说明

- UFile以HTTP RESTful API的形式对外提供服务，当访问不同的地域（Region）时，需要不同的访问域名。

- Endpoint分内网和外网访问域名。例如，华东1（上海）地域的外网Endpoint是cn-sh2.ufileos.com，内网Endpoint是internal-cn-sh2-01.ufileos.com。Region和Endpoint对照表请参考「访问域名和数据中心」。

- 您也可以通过绑定自定义域名或绑定CDN加速域名，将UFile的外网访问域名替换为您的自有域名。

### 通过外网访问UFile服务

外网指的是互联网。通过外网访问产生的流入流量（写）是免费的，流出流量（读）是收费的。

说明 UFile费用详情请参见[UFile服务价格页]()。

外网访问UFile有如下两种方式：

- 访问方式一：访问时以URL的形式来表示UFile的资源。UFile的URL构成如下：

`&lt;Schema&gt;://&lt;Bucket&gt;.&lt;外网Endpoint&gt;/&lt;Object&gt;`

- Schema：HTTP或者为HTTPS

- Bucket：UFile存储空间

- Endpoint：Bucket所在数据中心的访问域名，您需要填写外网Endpoint

- Object：上传到UFile上的文件

示例：如您的Region为华东1（cn-sh2），Bucket名称为abc，Object访问路径为myfile/aaa.txt，那么您的外网访问地址为

`abc.cn-sh2.ufileos.com/myfile/aaa.txt`

> 注意 UFile访问域名需携带Object访问路径才可以被访问，仅访问域名，如 abc.cn-sh2.ufileos.com，会有报错提示。
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
