# 从自建MySQL同步至阿里云消息队列Kafka版

通过数据传输服务DTS（Data Transmission Service），您可以将自建MySQL同步至消息队列Kafka版，扩展消息处理能力。

-   自建MySQL的数据库版本为5.1、5.5、5.6、5.7或8.0版本。
-   目标Kafka实例的版本为0.10.1.0-1.0.2版本。
-   目标Kafka实例中已创建用于接收同步数据的Topic，详情请参见[创建Topic](https://help.aliyun.com/document_detail/99952.html#title-7lc-gsy-p0n)。

[消息队列Kafka版](https://help.aliyun.com/document_detail/68151.html)是阿里云提供的分布式、高吞吐、可扩展的消息队列服务，针对开源的Apache Kafka提供全托管服务，彻底解决开源产品长期以来的痛点，您只需专注于业务开发，无需部署运维。消息队列Kafka版广泛用于日志收集、监控数据聚合、流式数据处理、在线和离线分析等大数据领域，已成为大数据生态中不可或缺的部分。

## 注意事项

-   DTS在执行全量数据初始化时将占用源库和目标库一定的读写资源，可能会导致数据库的负载上升，在数据库性能较差、规格较低或业务量较大的情况下（例如源库有大量慢SQL、存在无主键表或目标库存在死锁等），可能会加重数据库压力，甚至导致数据库服务不可用。因此您需要在执行数据同步前评估源库和目标库的性能，同时建议您在业务低峰期执行数据同步（例如源库和目标库的CPU负载在30%以下）。
-   如果源数据库没有主键或唯一约束，且所有字段没有唯一性，可能会导致目标数据库中出现重复数据。

## 功能限制

-   同步对象仅支持数据表，不支持非数据表的对象。
-   不支持自动调整同步对象，如果对同步对象中的数据表进行重命名操作，且重命名后的名称不在同步对象中，那么这部分数据将不再同步到到目标Kafka集群中。如需将修改后的数据表继续数据同步至目标Kafka集群中，您需要进行**修改同步对象**操作，详情请参见[新增同步对象](/cn.zh-CN/数据同步/同步作业管理/新增同步对象.md)。

## 消息格式

同步到Kafka集群中的数据以avro格式存储，schema定义详情请参见[DTS avro schema定义](https://github.com/LioRoger/subscribe_example/tree/master/avro)。

**说明：** 数据同步到Kafka集群后，您需要根据avro schema定义进行数据解析。

## 准备工作

[为自建MySQL创建账号并设置binlog](/cn.zh-CN/准备工作/为自建MySQL创建账号并设置binlog.md)

## 操作步骤

1.  购买数据同步作业，详情请参见[购买流程](/cn.zh-CN/快速入门/购买流程.md)。

    **说明：** 购买时，选择源实例为**MySQL**，目标实例为**Kafka**，选择同步拓扑为**单向同步**。

2.  登录[数据传输控制台](https://dts.console.aliyun.com/)。

3.  在左侧导航栏，单击**数据同步**。

4.  在同步作业列表页面顶部，选择同步的目标实例所属地域。

    ![选择地域](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7349459951/p50604.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。

6.  配置同步作业的源实例及目标实例信息。

    ![配置源和目标实例信息](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5720649951/p75455.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |无|同步作业名称|DTS会自动生成一个同步作业名称，建议配置具有业务意义的名称（无唯一性要求），便于后续识别。|
    |源实例信息|实例类型|根据源库部署位置选择，本文以**ECS上的自建数据库**为例介绍配置流程，其他类型的配置流程与该案例类似。|
    |实例地区|购买数据同步实例时选择的源实例地域信息，不可变更。|
    |ECS实例ID|选择自建MySQL所属的ECS实例ID。|
    |数据库类型|固定为**MySQL**，不可变更。|
    |端口|填入自建MySQL的数据库服务端口。|
    |数据库账号|填入自建MySQL的数据库账号，该账号需具备REPLICATION CLIENT、REPLICATION SLAVE、SHOW VIEW和所有同步对象的SELECT权限。|
    |数据库密码|填入该数据库账号对应的密码。|
    |目标实例信息|实例类型|选择**通过专线/VPN网关/智能接入网关接入的自建数据库**。 **说明：** 由于DTS暂时不支持直接选择消息队列Kafka版，此处将其作为自建Kafka来配置数据同步。 |
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |对端专有网络|选择目标Kafka实例所属的专有网络ID。您可以在Kafka实例的**基本信息**页面中查看到专有网络ID。![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5720649951/p74095.png) |
    |数据库类型|选择为**Kafka**。|
    |IP地址|填入Kafka实例**默认接入点**中的任意一个IP地址。 **说明：** 您可以在Kafka实例的**基本信息**页面中，获取**默认接入点**对应的IP地址。 |
    |端口|Kafka实例的服务端口，默认为9092。|
    |数据库账号|填入Kafka实例的用户名。 **说明：** 如果Kafka实例的实例类型为**VPC实例**，无需配置**数据库账号**和**数据库密码**。 |
    |数据库密码|填入该用户名对应的密码。|
    |Topic|单击右侧的**获取Topic列表**，然后在下拉框中选择具体的Topic。|
    |Kafka版本|根据Kafka实例版本，选择对应的版本信息。|
    |连接方式|根据业务及安全需求，选择**非加密连接**或**SCRAM-SHA-256**。|

7.  单击页面右下角的**授权白名单并进入下一步**。

8.  配置同步策略和同步对象信息。

    ![配置同步对象](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4030649951/p133327.png)

    |配置|说明|
    |--|--|
    |**同步到Kafka Partition策略**|根据业务需求选择同步的策略，详细介绍请参见[Kafka Partition同步策略说明](/cn.zh-CN/数据同步/同步作业管理/Kafka Partition同步策略说明.md)。|
    |同步对象|在**源库对象**区域框中，选择需要同步的对象（选择的粒度为表），然后单击![向右箭头](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/8502659951/p40698.png)图标将其移动到**已选对象**区域框中。**说明：** DTS会自动将表名映射为配置同步的源和目标实例信息时选择的Topic名称。如果需要更换同步的目标Topic，请参见步骤9。 |

9.  在**已选择对象**区域框中，将鼠标指针放置在目标Topic名上，然后单击Topic名后出现的**编辑**，在弹出的对话框中设置源表在目标Kafka实例中的Topic名称、Topic的Partition数量、Partition Key等信息。

    ![设置topic](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4030649951/p133332.png)

    |配置|说明|
    |--|--|
    |**数据库表名称**|设置源表同步到的目标Topic名称。**警告：** 设置的Topic名称必须在目标Kafka实例中真实存在，否则将导致数据同步失败。 |
    |**过滤条件**|    -   过滤条件支持标准的SQL WHERE语句（仅支持`=`、`!=`、`<`和`>`操作符），只有满足WHERE条件的数据才会被同步到目标Topic。本案例填入`id>1000`。
    -   过滤条件中如需使用引号，请使用单引号（'），例如`address in('hangzhou','shanghai')`。 |
    |**设置新Topic的Partition数量**|本场景中，目标Kafka为消息队列Kafka实例，暂不支持该功能，无需配置本参数。|
    |设置Partition Key|当您在步骤8中选择同步策略为**按主键的hash值投递到不同Partition**时，您可以配置本参数，指定单个或多个列作为Partition Key来计算Hash值，DTS将根据计算得到的Hash值将不同的行投递到目标Topic的各Partition中。|

10. 上述配置完成后单击页面右下角的**下一步**。

11. 配置同步初始化的高级配置信息。

    ![Kafka同步初始化高级配置](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4030649951/p87942.png)

    |配置|说明|
    |--|--|
    |**同步初始化**|默认选择**结构初始化**和**全量数据初始化**，DTS会在增量数据同步之前，将源库中待同步对象的结构和存量数据，同步到目标库。|
    |**过滤选项**|默认选择**忽略增量同步阶段的 DDL**，即增量同步阶段源库执行的DDL操作不会被DTS同步至目标库。|

12. 上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：**

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![提示](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/8502659951/p47468.png)图标，查看失败详情。根据提示修复后，重新进行预检查。
13. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，数据同步作业正式开始。

    您可以在数据同步页面，查看数据同步状态。

    ![查看数据同步状态](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4030649951/p39871.png)


