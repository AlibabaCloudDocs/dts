# 【3月】DTS产品发布记录

数据传输服务DTS（Data Transmission Service）为您提供最新产品发布记录，包括新功能、功能优化以及缺陷修复，方便您了解最新的产品发布动态。

## 3月11日发布动态

|类型|重点内容|具体内容|
|--|----|----|
|新功能|新版控制台|源为RDS SQL Server的数据迁移和同步链路，在原先基于SQL Server日志解析进行数据迁移或同步的基础上，新增CDC的模式，解决了日志截断导致迁移或同步任务中断，不支持迁移或同步堆表、无主键表等问题。|
|支持配置如下迁移链路：-   Db2 for LUW 迁移至云原生数仓AnalyticDB PostgreSQL、MySQL（自建MySQL、RDS MySQL）。
-   Db2 for i 迁移至MySQL（自建MySQL、RDS MySQL）。

**说明：** 具体配置步骤，请参见[从自建Db2迁移至RDS MySQL](/cn.zh-CN/数据迁移/从自建数据库迁移至阿里云/源库为Db2/从自建Db2迁移至RDS MySQL.md)。 |
|功能优化和缺陷修复|优化迁移或同步链路。|源为[Redis企业版](/cn.zh-CN/产品简介/云数据库Redis企业版/企业版简介.md)的同步链路。|
|优化源为PolarDB-X的全量迁移功能。|

本次发布预计为3月11日22点至24点。期间，如果您的DTS实例出现异常，请[提交工单](https://selfservice.console.aliyun.com/ticket/category/dts/today)咨询。

## 3月4日发布动态

|重点内容|具体内容|
|----|----|
|优化数据迁移和同步任务的一键诊断功能。|数据迁移和同步任务的一键诊断功能，在诊断源库性能时，增加[数据库自治服务DAS（Database Autonomy Service）]()的异常诊断内容。**说明：** 源实例需已接入DAS，且数据类型为MySQL。 |
|支持选择目标库是否执行DMS Online DDL。|如果使用[数据管理DMS（Data Management Service）]()对源库执行Online DDL（在线DDL），您可以在DTS控制台选择是否迁移或同步Online DDL产生的临时表数据。|
|正在升级数据订阅任务的查看订阅数据功能。|为缩短订阅数据查询的时间，DTS正在升级数据订阅任务的查看订阅数据功能。升级期间，功能入口暂时关闭。|
|优化OpenAPI的系统参数RegionId。|调用OpenAPI时，支持根据系统参数RegionId过滤DTS实例。|

本次发布预计为3月4日22点至24点。期间，如果您的DTS实例出现异常，请[提交工单](https://selfservice.console.aliyun.com/ticket/category/dts/today)咨询。

