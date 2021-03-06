# 【1月】DTS产品发布记录

数据传输服务DTS（Data Transmission Service）为您提供最新产品发布记录，包括新功能、功能优化以及缺陷修复，方便您了解最新的产品发布动态。

## 1月28日发布动态

DTS产品功能优化和缺陷修复如下：

|重点内容|具体内容|
|----|----|
|优化迁移链路。|优化自建PostgreSQL或RDS PostgreSQL迁移至自建PostgreSQL或RDS PostgreSQL的链路，具体优化点如下：-   支持选择迁移对象为库。
-   增量迁移时支持同步的DDL操作为：
    -   CREATE TABLE、DROP TABLE
    -   ALTER TABLE（包括RENAME TABLE、ADD COLUMN、ADD COLUMN DEFAULT、ALTER COLUMN TYPE、DROP COLUMN、ADD CONSTRAINT、ADD CONSTRAINT CHECK、ALTER COLUMN DROP DEFAULT）
    -   CREATE INDEX ON TABLE、DROP INDEX
    -   DROP RULE
    -   CREATE SEQUENCE、ALTER SEQUENCE RENAME TO、DROP SEQUENCE

**说明：** 优化点会在上海地区新建的迁移实例中体现。 |
|优化新版控制台交互和文案。|新版控制台新增迁移和同步链路：-   迁移：PolarDB MySQL、自建Oracle、自建SQL Server、RDS SQL Server为源的链路。
-   同步：PolarDB MySQL、自建SQL Server、RDS SQL Server为源的链路。 |
|优化OpenAPI报错提示。|优化如下OpenAPI的报错提示：-   DescribeConnectionStatus
-   DescribeEndpointSwitchStatus
-   [DescribeSynchronizationJobStatusList](/cn.zh-CN/API参考/数据同步/查询一个或多个同步实例状态.md) |

本次发布预计为1月28日22点至24点。期间，如果您的DTS实例出现异常，请[提交工单](https://selfservice.console.aliyun.com/ticket/category/redis/today)咨询。

## 1月21日发布动态

|类型|重点内容|具体内容|
|--|----|----|
|新功能|新增同步链路。|支持RDS SQL Server同步到云原生数据仓库AnalyticDB MySQL。|
|选择目标库是否执行DMS Online DDL。|如果使用数据管理DMS（Data Management Service）对源库执行Online DDL（在线DDL），您可以在DTS控制台选择是否同步这类变更：-   是：DMS Online DDL变更将同步到目标库。
-   否：该DMS Online DDL变更不会同步到目标库，只会同步原始DDL到目标库。 |
|配置目标库对象名称大小写策略。|支持配置同步到目标库库表名的大小写策略配置，以满足目标库英文字母大小写要求。|
|功能优化和缺陷修复|优化迁移或同步链路。|优化RDS MySQL实例间的双向同步，目标库为Elasticsearch或Datahub的同步链路，以及涉及自建MongoDB和阿里云MongoDB实例的迁移链路。|
|优化目标已存在表的处理模式。|目标实例为Elasticsearch的同步链路，如果目标已存在表，支持选择处理模式为**忽略报错并继续执行** 。|
|释放过期包年包月实例。|支持在控制台释放过期的包年包月实例。|
|修复增量数据服务启动失败。|修复特殊情况下增量数据服务（例如增量数据迁移）启动失败的缺陷。|
|源或目标实例升级规格对时间要求。|当迁移或同步时，源或目标实例如需升级规格，需等到迁移或同步任务进入增量迁移和同步中状态时才能升级规格。|
|其他。|优化实例容灾功能。|

本次发布时间预计为1月21日22点至24点。期间，如果您的DTS实例出现异常，请[提交工单](https://selfservice.console.aliyun.com/ticket/category/dts/today)咨询。

