# 【2月】DTS产品发布记录

数据传输服务DTS（Data Transmission Service）为您提供最新产品发布记录，包括新功能、功能优化以及缺陷修复，方便您了解最新的产品发布动态。

## 2月25日发布动态

|重点内容|具体内容|
|----|----|
|优化目标实例为Datahub的同步链路。|优化自建MySQL、PolarDB MySQL、PolarDB-X同步至Datahub的链路。|
|优化全量迁移和全量初始化功能。|在涉及自建MySQL、自建Oracle、PolarDB-X、自建SQL Server、MaxCompute的迁移或同步链路中，优化全量迁移、全量初始化功能。|
|优化数据订阅内核。|优化数据订阅内核。|
|优化包年包月实例的释放方式。|在**同步作业列表**或**数据订阅列表**页释放包年包月实例时，单击**释放同步**或**释放订阅**，您可以根据退款提示，跳转至**退订管理**页面，来释放包年包月实例。更多信息，请参见[释放包年包月实例](/cn.zh-CN/实例管理/释放实例.md)。|

本次发布预计为2月25日22点至24点。期间，如果您的DTS实例出现异常，请[提交工单](https://selfservice.console.aliyun.com/ticket/category/redis/today)咨询。

## 2月4日发布动态

|类型|重点内容|具体内容|
|--|----|----|
|新功能|新增数据集成链路。|目标实例为云原生数据仓库AnalyticDB MySQL的数据集成任务，支持选择调度策略为基于时间戳的增量模式。|
|新版控制台新增迁移链路。|新版控制台新增迁移链路：-   自建Oracle迁移至PolarDB-O集群。具体配置步骤，请参见[从自建Oracle迁移至PolarDB-O集群（迁移结构）](/cn.zh-CN/数据迁移/从自建数据库迁移至阿里云/源库为Oracle/从自建Oracle迁移至PolarDB-O集群（迁移结构）.md)、[从自建Oracle迁移至PolarDB-O集群（迁移数据）](/cn.zh-CN/数据迁移/从自建数据库迁移至阿里云/源库为Oracle/从自建Oracle迁移至PolarDB-O集群（迁移数据）.md)。
-   PolarDB-O集群间迁移。具体配置步骤，请参见[PolarDB-O集群间的数据迁移](/cn.zh-CN/数据迁移/同一阿里云账号实例间迁移/PolarDB-O集群间的数据迁移.md)。
-   PolarDB-O集群迁移至自建Oracle。

**说明：** PolarDB-O集群需以**有公网IP的自建数据库**接入。 |
|功能优化和缺陷修复|优化迁移或同步链路。|迁移链路：-   源为自建Oracle的链路。
-   涉及自建MongoDB和阿里云MongoDB实例的链路。

同步链路：目标库为Elasticsearch或Datahub的链路。 |
|修复延时不准确的缺陷|修复了Redis企业版单向或双向同步任务在增量数据同步的初始阶段，延时信息不准确的缺陷。|

本次发布预计为2月4日22点至24点。期间，如果您的DTS实例出现异常，请[提交工单](https://selfservice.console.aliyun.com/ticket/category/redis/today)咨询。

