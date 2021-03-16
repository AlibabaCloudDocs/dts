# Kafka Partition迁移策略说明

在配置迁移到Kafka的数据迁移任务时，为提升迁移的性能，您可以调整Kafka Partition（分区）同迁移略，例如根据Hash结果将数据迁移至不同的Partition。

## Hash算法

DTS采用Java默认的hashcode算法来计算Hash值。

## 配置方法

您需要在配置数据迁移任务的**迁移类型及列表**环节中配置Kafka Partition迁移策略。配置案例请参见[从自建Oracle迁移至阿里云消息队列Kafka](/cn.zh-CN/数据迁移/从自建数据库迁移至阿里云/源库为Oracle/从自建Oracle迁移至阿里云消息队列Kafka.md)，更多相关案例请参见[DTS数据迁移方案概览](/cn.zh-CN/数据迁移/DTS数据迁移方案概览.md)。

**警告：** 数据迁移任务启动后，请勿修改目标Topic的Partition数量，否则将导致数据迁移失败。

## 策略说明

|策略名称|说明|优缺点|
|----|--|---|
|**全部递到Partition 0**|将所有数据和DDL信息都投递到目标Topic的Partition 0。|-   优势：所有对象的创建、更改顺序都和源库保持一致。
-   缺点：性能一般。 |
|**按库名+表名的hash值投递到不同Partition**|将库名与表名合并作为Partition Key来计算Hash值，然后将各个表的数据和DDL信息投递到目标Topic中的各个Partition。 **说明：**

-   同一个表的数据和DDL信息会投递到同一个Partition。
-   与表无关的DDL信息（例如CREATE DATABASE）则投递到Partition 0。

|-   优势：单个表的创建、变更顺序和源端保持一致，性能较好。
-   缺点：由于不同的表投递到不同的Partition，表与表之间的先后顺序无法保证。 |
|**按主键的hash值投递到不同Partition**|将表中的列（默认为主键，如果无主键则使用唯一键）作为Partition Key来计算Hash值，然后将不同的行投递到目标Topic的各个Partition中。您也可以指定单个或多个列作为Partition Key来计算Hash值。 **说明：**

-   此策略下，DDL信息默认投递到目标Topic的Partition 0中。
-   如果表没有主键和唯一键，DTS会将其数据和DDL信息都投递到目标Topic的Partition 0。

|-   优势：该策略性能最好。
-   缺点：只能保证单条记录的变更的先后顺序，无主键表以及多张表之间的先后顺序无法保证。 |

