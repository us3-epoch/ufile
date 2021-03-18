
# 下载与配置使用

## 源码下载

* 下载源码

```
git clone https://github.com/us3-epoch/ClickHouse
```

* 切到指定分支

```
git checkout us3_support_v20.8.7.15-lts
```

* 下载依赖的子模块

```
git submodule update --init --recursive
```

## 编译

clickhouse编译依赖gcc/llvm，cmake，ninja。如使用gcc，请确保版本在10及以上。可按[官方说明](https://clickhouse.tech/docs/en/development/build/)准备编译环境。

编译：

```bash
cd ClickHouse
mkdir build
cd build
cmake ..
ninja
```

*注：如使用gcc，请将`cmake ..`替换为`cmake -DENABLE_EMBEDDED_COMPILER=0 -DUSE_INTERNAL_LLVM_LIBRARY=0 -DWERROR=0 ..`

## 配置与使用

如需使用us3作为后端存储，需在配置文件中增加disk配置。配置文件的详细设置请参考[官方链接](https://clickhouse.tech/docs/en/operations/server-configuration-parameters/settings/)

在配置文件的disks中增加如下配置：

```xml
        <disks>
            <your_name>
                <type>us3</type>
                <endpoint>ufile.cn-north-02.ucloud.cn</endpoint>
                <bucket>your-bucket</bucket>
                <access_key>***************</access_key>
                <secret_key>***************</secret_key>
                <prefix>/clickhouse/</prefix>
            </your_name>
        </disks>
```

policies中增加如下配置:

```xml
        <policies>
            <your_name>
                <volumes>
                    <main>
                        <disk>your_disk_name</disk>
                    </main>
                </volumes>
            </your_name>
        </policies>
```

创建表时增加如下语句

```sql
SETTINGS your_setting,
storage_policy = 'your-policy-name';
```

即可创建使用us3作为存储后端的表。

可通过如下命令查看策略是否创建成功。

```
clickhouse-client

select * from system.storage_policies
```

输出如下（省略部分内容）：

```
┌─policy_name─┬─volume_name─┬─volume_priority─┬─disks────────────┬─volume_type─┬─max_data_part_size─┬─move_factor─┐
│ default     │ default     │               1 │ ['default']      │ JBOD        │                  0 │           0 │
│ testpolicy  │ main        │               1 │ ['testdiskname'] │ JBOD        │                  0 │         0.1 │
└─────────────┴─────────────┴─────────────────┴──────────────────┴─────────────┴────────────────────┴─────────────┘
```
