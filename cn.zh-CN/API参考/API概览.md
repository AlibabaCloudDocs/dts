# API概览

数据传输服务DTS提供以下API接口。

## 数据迁移

|API|描述|
|:--|:-|
|[CreateMigrationJob](/cn.zh-CN/API参考/数据迁移/购买迁移实例.md)|购买数据迁移实例。|
|[ConfigureMigrationJob](/cn.zh-CN/API参考/数据迁移/配置迁移实例.md)|配置数据迁移实例。|
|[StartMigrationJob](/cn.zh-CN/API参考/数据迁移/启动迁移实例.md)|启动数据迁移实例。|
|[SuspendMigrationJob](/cn.zh-CN/API参考/数据迁移/暂停迁移实例.md)|暂停数据迁移实例。|
|[DescribeMigrationJobStatus](/cn.zh-CN/API参考/数据迁移/查询一个迁移实例状态.md)|查询一个数据迁移实例的任务状态。|
|[DescribeMigrationJobDetail](/cn.zh-CN/API参考/数据迁移/查询一个迁移实例详情.md)|查询一个数据迁移实例的任务详情。|
|[StopMigrationJob](/cn.zh-CN/API参考/数据迁移/结束迁移实例.md)|结束数据迁移实例。|
|[DeleteMigrationJob](/cn.zh-CN/API参考/数据迁移/结束迁移实例.md)|释放数据迁移实例。|

## 数据订阅

|API|描述|
|:--|:-|
|[CreateSubscriptionInstance](/cn.zh-CN/API参考/数据订阅/购买订阅实例.md)|购买数据订阅实例。|
|[ConfigureSubscriptionInstance](/cn.zh-CN/API参考/数据订阅/配置订阅通道.md)|配置数据订阅实例。|
|[StartSubscriptionInstance](/cn.zh-CN/API参考/数据订阅/启动订阅实例.md)|启动数据订阅实例。|
|[ModifySubscriptionObject](/cn.zh-CN/API参考/数据订阅/修改订阅对象.md)|修改订阅对象。|
|[CreateConsumerGroup](/cn.zh-CN/API参考/数据订阅/管理消费组/创建消费组.md)|创建消费组。|
|[DescribeConsumerGroup](/cn.zh-CN/API参考/数据订阅/管理消费组/查询消费组详情.md)|查询消费组详情。|
|[ModifyConsumerGroupPassword](/cn.zh-CN/API参考/数据订阅/管理消费组/修改消费组密码.md)|修改消费组密码。|
|[DeleteConsumerGroup](/cn.zh-CN/API参考/数据订阅/管理消费组/删除消费组.md)|删除消费组。|
|[ModifyConsumptionTimestamp](/cn.zh-CN/API参考/数据订阅/修改消费时间点.md)|修改消费时间点。|
|[DescribeSubscriptionInstances](/cn.zh-CN/API参考/数据订阅/查看订阅实例列表.md)|查询数据订阅实例列表。|
|[DescribeSubscriptionInstanceStatus](https://help.aliyun.com/document_detail/49441.html)|查询数据订阅实例状态。|
|[DeleteSubscriptionInstance](/cn.zh-CN/API参考/数据订阅/释放订阅实例.md)|释放数据订阅实例。|

## 数据同步

|API|描述|
|:--|:-|
|[CreateSynchronizationJob](/cn.zh-CN/API参考/数据同步/购买同步实例.md)|购买数据同步实例。|
|[ConfigureSynchronizationJob](/cn.zh-CN/API参考/数据同步/配置同步实例.md)|配置数据同步实例。|
|[StartSynchronizationJob](/cn.zh-CN/API参考/数据同步/启动同步实例.md)|启动数据同步实例。|
|[SuspendSynchronizationJob](/cn.zh-CN/API参考/数据同步/暂停同步实例.md)|暂停数据同步实例。|
|[ResetSynchronizationJob](/cn.zh-CN/API参考/数据同步/清空同步实例配置.md)|清空数据同步实例配置。|
|[ModifySynchronizationObject](/cn.zh-CN/API参考/数据同步/修改同步对象.md)|修改同步对象。|
|[DescribeSynchronizationObjectModifyStatus](https://help.aliyun.com/document_detail/49452.html)|查询同步对象变更状态。|
|[SwitchSynchronizationEndpoint]()|更改数据库连接信息。|
|[DescribeEndpointSwitchStatus](/cn.zh-CN/API参考/数据同步/查询更改数据库连接信息的任务执行状态.md)|查询更改数据库连接信息的任务执行状态。|
|[ConfigureSynchronizationJobReplicatorCompare](/cn.zh-CN/API参考/数据同步/配置全镜像匹配开关.md)|配置一个同步实例的全镜像匹配开关。|
|[DescribeSynchronizationJobReplicatorCompare](/cn.zh-CN/API参考/数据同步/查询全镜像匹配开关状态.md)|查询一个同步实例的全镜像匹配开关状态。|
|[DescribeSynchronizationJobs](https://help.aliyun.com/document_detail/49454.html)|查询数据同步实例列表。|
|[DescribeSynchronizationJobStatusList](/cn.zh-CN/API参考/数据同步/查询一个或多个同步实例状态.md)|查询一个或多个同步实例状态。|
|[DescribeSynchronizationJobStatus](/cn.zh-CN/API参考/数据同步/查询一个数据同步实例状态.md)|查询一个数据同步实例状态。|
|[DeleteSynchronizationJob](/cn.zh-CN/API参考/数据同步/释放同步实例.md)|释放数据同步实例。|

## 网络设置

|API|描述|
|---|--|
|[DescribeDTSIP](/cn.zh-CN/API参考/网络设置/查询DTS的IP地址.md)|查询DTS服务的IP地址。|

## 监控报警

|API|描述|
|---|--|
|[ConfigureMigrationJobAlert](/cn.zh-CN/API参考/监控报警/配置迁移实例的监控报警.md)|配置数据迁移实例的监控报警。|
|[DescribeMigrationJobAlert](/cn.zh-CN/API参考/监控报警/查询迁移实例的监控报警.md)|查询数据迁移实例的监控报警设置。|
|[ConfigureSubscriptionInstanceAlert](/cn.zh-CN/API参考/监控报警/配置订阅实例的监控报警.md)|配置数据订阅实例的监控报警。|
|[DescribeSubscriptionInstanceAlert](/cn.zh-CN/API参考/监控报警/查询订阅实例的监控报警.md)|查询数据订阅实例的监控报警设置。|
|[ConfigureSynchronizationJobAlert](/cn.zh-CN/API参考/监控报警/配置同步实例的监控报警.md)|配置数据同步实例的监控报警。|
|[DescribeSynchronizationJobAlert](/cn.zh-CN/API参考/监控报警/查询同步实例的监控报警.md)|查询数据同步实例的监控报警设置。|

## 标签管理

|API|描述|
|---|--|
|[TagResources](/cn.zh-CN/API参考/标签管理/创建标签.md)|为一个或多个迁移、同步和订阅实例绑定标签。|
|[ListTagResources](/cn.zh-CN/API参考/标签管理/查询标签.md)|查询迁移、同步和订阅实例绑定的标签，也可以查询标签所绑定的实例。|
|[UntagResources](/cn.zh-CN/API参考/标签管理/解绑标签.md)|为迁移、同步和订阅实例解绑标签。|

## 任务管理

|API|描述|
|---|--|
|[ShieldPrecheck](/cn.zh-CN/API参考/任务管理/屏蔽预检查告警项.md)|屏蔽迁移或同步时导致预检查失败的告警项。|

## 更多信息

-   [API资源导航](https://developer.aliyun.com/)
-   [API错误中心](https://error-center.aliyun.com/status/product/Dts)

