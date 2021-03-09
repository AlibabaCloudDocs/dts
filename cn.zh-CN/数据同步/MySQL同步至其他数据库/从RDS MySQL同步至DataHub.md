# 从RDS MySQL同步至DataHub

阿里云流式数据服务DataHub是流式数据（Streaming Data）的处理平台，提供对流式数据的发布、订阅和分发功能，让您可以轻松构建基于流式数据的分析和应用。通过数据传输服务DTS（Data Transmission Service），您可以将RDS MySQL同步至DataHub，帮助您快速实现使用流计算等大数据产品对数据实时分析。

-   DataHub实例的地域为华东1、华东2、华北2或华南1。
-   DataHub实例中，已创建用作接收同步数据的项目（Project），详情请参见[创建项目](~~206546~~)。
-   RDS MySQL中待同步的表需具备主键或唯一约束。

## 功能限制

-   不支持全量数据初始化，即DTS不会将源RDS实例中同步对象的存量数据同步至目标DataHub实例。
-   仅支持表级别的数据同步。
-   不支持新增列的数据同步，即源数据表新增了某个列，该列的数据不会同步至目标DataHub实例。
-   数据同步的过程中，请勿对源库中待同步的表执行DDL变更，否则会导致同步失败。

## 支持同步的SQL操作

INSERT、UPDATE、DELETE。

## 操作步骤

1.  购买数据同步作业，详情请参见[购买流程](/cn.zh-CN/快速入门/购买流程.md)。

    **说明：** 购买时，选择源实例为**MySQL**、目标实例为**DataHub**，并选择同步拓扑为**单向同步**。

2.  登录[数据传输控制台](https://dts.console.aliyun.com/)。

3.  在左侧导航栏，单击**数据同步**。

4.  在同步作业列表页面顶部，选择同步的目标实例所属地域。

    ![选择地域](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p50604.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。

6.  配置同步作业的源实例及目标实例信息。

    ![DataHub同步源目信息配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3620649951/p49690.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |无|同步作业名称|DTS会自动生成一个同步作业名称，建议配置具有业务意义的名称（无唯一性要求），便于后续识别。|
    |源实例信息|实例类型|根据源库的部署位置进行选择，本文以**RDS实例**为例介绍配置流程。 **说明：** 如果源库为自建MySQL数据库，您还需要执行相应的准备工作，详情请参见[准备工作概览](/cn.zh-CN/准备工作/准备工作概览.md)。 |
    |实例地区|购买数据同步实例时选择的源实例地域信息，不可变更。|
    |实例ID|选择作为数据同步源的RDS实例ID。|
    |数据库账号|填入源RDS的数据库账号。 **说明：** 当源RDS实例的数据库类型为**MySQL 5.5**或**MySQL 5.6**时，无需配置**数据库账号**和**数据库密码**。 |
    |数据库密码|填入数据库账号对应的密码。|
    |连接方式|根据需求选择**非加密连接**或**SSL安全连接**。如果设置为**SSL安全连接**，您需要提前开启RDS实例的SSL加密功能，详情请参见[设置SSL加密](https://help.aliyun.com/document_detail/96120.html)。|
    |目标实例信息|实例类型|固定为**DataHub**，不可变更。|
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |Project|选择DataHub实例的**Project**。|

7.  单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到源RDS实例的白名单中，用于保障DTS服务器能够正常连接源RDS实例。

8.  配置同步策略和同步对象。

    ![配置同步对象](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3230649951/p49691.png)

    |配置|说明|
    |:-|:-|
    |同步初始化|勾选**结构初始化**。 **说明：** 勾选**结构初始化**后，在数据同步作业的初始化阶段，DTS会将同步对象的结构信息（例如表结构）同步至目标DataHub实例。 |
    |选择同步对象|在源库对象框中单击待迁移的对象，然后单击![向右小箭头](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p40698.png)将其移动至已选择对象框。

**说明：**

    -   同步对象的选择粒度为表。
    -   默认情况下，同步对象的名称保持不变。如果您需要改变同步对象在目标实例中的名称，需要使用对象名映射功能，详情请参见[设置同步对象在目标实例中的名称](/cn.zh-CN/数据同步/同步作业管理/设置同步对象在目标实例中的名称.md)。 |
    |选择附加列规则|DTS在将数据同步到DataHub时，会在同步的目标Topic中添加一些附加列。如果附加列和目标Topic中已有的列出现名称冲突将会导致数据同步失败。您需要根据业务需求选择**是否启用新的附加列规则**为**是**或**否**。 **警告：** 在选择附加列规则前，您需要评估附加列和目标Topic中已有的列是否会出现名称冲突。关于附加列的规则和定义说明，请参见[附加列名称和定义说明](/cn.zh-CN/数据同步/同步作业管理/修改数据同步的附加列规则.md)。 |

9.  将鼠标指针放置在**已选择对象**框中待同步的Topic名上，单击对象后出现的**编辑**，然后在弹出的对话框中设置Shardkey（即用于分区的key）。

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3230649951/p69998.gif)

10. 上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：**

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![提示](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p47468.png)图标，查看失败详情。根据提示修复后，重新进行预检查。
11. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，同步作业将正式开始。

12. 等待同步作业的链路初始化完成，直至处于**同步中**状态。

    您可以在数据同步页面，查看数据同步作业的状态。

    ![查看同步作业状态](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1349459951/p41059.png)


## Topic结构定义说明

DTS在将数据变更同步至DataHub实例的Topic时，目标Topic中除了存储变更数据外，还会新增一些附加列用于存储元信息，示例如下。

**说明：** 本案例中的业务字段为`id`、`name`、`address`，由于在配置数据同步时选用的是旧版附加列规则，DTS会为业务字段添加`dts_`的前缀。

![Topic定义](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4230649951/p69959.png)

结构定义说明：

|旧版附加列名称|新版附加列名称|说明|
|:------|-------|:-|
|`dts_record_id`|`new_dts_sync_dts_record_id`|增量日志的记录ID，为该日志唯一标识。 **说明：**

-   正常情况下是全局唯一自增的，在容灾的情况下会有回退且无法保证自增和唯一。
-   如果增量日志的操作类型为UPDATE，那么增量更新会被拆分成两条记录（分别记录更新前和更新后的值），且`dts_record_id`的值相同。 |
|`dts_operation_flag`|`new_dts_sync_dts_operation_flag`|操作类型，取值： -   I：INSERT操作。
-   D：DELETE操作。
-   U：UPDATE操作。 |
|`dts_instance_id`|`new_dts_sync_dts_instance_id`|数据库的server ID。|
|`dts_db_name`|`new_dts_sync_dts_db_name`|数据库名称。|
|`dts_table_name`|`new_dts_sync_dts_table_name`|表名。|
|`dts_utc_timestamp`|`new_dts_sync_dts_utc_timestamp`|操作时间戳，即binlog的时间戳（UTC 时间）。|
|`dts_before_flag`|`new_dts_sync_dts_before_flag`|所有列的值是否更新前的值，取值：Y或N。|
|`dts_after_flag`|`new_dts_sync_dts_after_flag`|所有列的值是否更新后的值，取值：Y或N。|

## 关于dts\_before\_flag和dts\_after\_flag的补充说明

对于不同的操作类型，增量日志中的`dts_before_flag`和`dts_after_flag`定义如下：

-   INSERT

    当操作类型为INSERT时，所有列的值为新插入的值，即为更新后的值，所以`dts_before_flag`取值为N，`dts_after_flag`取值为Y，示例如下。

    ![INSERT操作](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4230649951/p69960.png)

-   UPDATE

    当操作类型为UPDATE时，DTS会将UPDATE操作拆为两条增量日志。这两条增量日志的`dts_record_id`、`dts_operation_flag`及`dts_utc_timestamp`对应的值相同。

    第一条增量日志记录了更新前的值，所以`dts_before_flag`取值为Y，`dts_after_flag`取值为N。第二条增量日志记录了更新后的值，所以 `dts_before_flag`取值为N，`dts_after_flag`取值为Y，示例如下。

    ![UPDATE操作](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4230649951/p69955.png)

-   DELETE

    当操作类型为DELETE时，增量日志中所有的列值为被删除的值，即列值不变，所以`dts_before_flag`取值为Y， `dts_after_flag`取值为N，示例如下。

    ![DELETE操作](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4230649951/p69961.png)


## 后续操作

配置完数据同步作业后，您可以对同步到DataHub实例中的数据执行计算分析。更多详情，请参见[阿里云实时计算](https://help.aliyun.com/document_detail/110778.html)。

