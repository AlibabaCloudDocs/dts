# MigrationReserved参数说明

在调用ConfigureMigrationJob或ConfigureSynchronizationJob接口配置任务时，您可以传入本参数实现特殊需求（例如是否自动启动预检查）。

## 注意事项

如需正常使用该参数，您需要使用最新版本的SDK，详情请参见[SDK下载](/cn.zh-CN/SDK参考/SDK下载.md)。

## 相关接口

-   [配置迁移任务](/cn.zh-CN/API参考/数据迁移/配置迁移任务.md)
-   [配置同步实例](/cn.zh-CN/API参考/数据同步/配置同步实例.md)

## MigrationReserved定义说明

MigrationReserved（DTS预留参数）的取值类型为String，传入数据的格式为JSON，详细定义如下所示。

|参数|适用的接口|说明|
|--|-----|--|
|autoStartModulesAfterConfig|-   [配置迁移任务](/cn.zh-CN/API参考/数据迁移/配置迁移任务.md)
-   [配置同步实例](/cn.zh-CN/API参考/数据同步/配置同步实例.md)

|启动控制参数，取值： -   all：完成DTS任务的配置后，自动启动预检查模块及其后续所有模块。
-   none：完成DTS任务的配置后，不启动预检查等模块，即需要手动启动任务。

默认值为all。 |
|targetTableMode|-   [配置迁移任务](/cn.zh-CN/API参考/数据迁移/配置迁移任务.md)
-   [配置同步实例](/cn.zh-CN/API参考/数据同步/配置同步实例.md)

|同名对象存在性检查，取值： -   0：报错并拦截。
-   2：忽略报错并继续执行。 |
|srcRedisType|[配置同步实例](/cn.zh-CN/API参考/数据同步/配置同步实例.md)|取值固定为enterprise：阿里云Redis企业版实例。 **说明：** 本参数仅在Redis企业版实例间的数据同步场景下适用。 |
|destRedisType|

## 请求示例

```
{
    "autoStartModulesAfterConfig": "none",
    "targetTableMode": 2
}
```

