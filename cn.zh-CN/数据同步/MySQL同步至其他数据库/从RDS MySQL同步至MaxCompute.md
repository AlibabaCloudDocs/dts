# 从RDS MySQL同步至MaxCompute

大数据计算服务（MaxCompute，原名ODPS）是一种快速、完全托管的EB级数据仓库解决方案。通过数据传输服务DTS（Data Transmission Service），您可以将MySQL的数据同步至MaxCompute，帮助您快速搭建数据实时分析系统。

您已完成以下操作：

-   [开通MaxCompute](https://help.aliyun.com/document_detail/58226.html)
-   [创建项目](https://help.aliyun.com/document_detail/27815.html)

## 注意事项

-   DTS在执行全量数据初始化时将占用源库和目标库一定的读写资源，可能会导致数据库的负载上升，在数据库性能较差、规格较低或业务量较大的情况下（例如源库有大量慢SQL、存在无主键表或目标库存在死锁等），可能会加重数据库压力，甚至导致数据库服务不可用。因此您需要在执行数据同步前评估源库和目标库的性能，同时建议您在业务低峰期执行数据同步（例如源库和目标库的CPU负载在30%以下）。
-   仅支持表级别的数据同步。
-   数据同步期间，请勿对源库的同步对象使用gh-ost或pt-online-schema-change等类似工具执行在线DDL变更，否则会导致同步失败。
-   由于MaxCompute不支持主键约束，当DTS在同步数据时因网络等原因触发重传，可能会导致MaxCompute中出现重复记录。

## 源库支持的实例类型

执行数据同步操作的MySQL数据库支持以下实例类型：

-   ECS上的自建数据库
-   通过专线、VPN网关或智能网关接入的自建数据库
-   通过数据库网关接入的自建数据库
-   同一或不同阿里云账号下的RDS MySQL实例

本文以**RDS实例**为例介绍配置流程，当源库为其他实例类型时，配置流程与该案例类似。

**说明：** 如果源库为自建MySQL数据库，您还需要执行相应的准备工作，详情请参见[准备工作概览](/cn.zh-CN/准备工作/准备工作概览.md)。

## 支持同步的SQL操作

-   DDL操作：ADD COLUMN

    **说明：** 仅支持下述数据类型：INTEGER、BIGINTEGER、IGINT、LONGSTRING、YEAR、TIME、DATA、TIME STAMP、DATA TIME BYTE BOOLEAN DECIMAL、DOUBLE、FLOAT。

-   DML操作：INSERT、UPDATE、DELETE

## 同步过程介绍

1.  结构初始化。

    DTS将源库中待同步表的结构定义信息同步至MaxCompute中，初始化时DTS会为表名增加\_base后缀。例如源表为customer，那么MaxCompute中的表即为customer\_base。

2.  全量数据初始化。

    DTS将源库中待同步表的存量数据，全部同步至MaxCompute中的目标表名\_base表中（例如从源库的customer表同步至MaxCompute的customer\_base表），作为后续增量同步数据的基线数据。

    **说明：** 该表也被称为全量基线表。

3.  增量数据同步。

    DTS在MaxCompute中创建一个增量日志表，表名为同步的目标表名\_log，例如customer\_log，然后将源库产生的增量数据实时同步到该表中。

    **说明：** 关于增量日志表结构的详细信息，请参见[增量日志表结构定义说明](#section_5kn_hla_atd)。


## 操作步骤

**警告：** 为保障DTS同步账号能够授权成功，请使用主账号完成下述操作步骤。

1.  购买数据同步作业，详情请参见[购买流程](/cn.zh-CN/快速入门/购买流程.md)。

    **说明：** 购买时，选择源实例为**MySQL**，目标实例为**MaxCompute**，并选择同步拓扑为**单向同步**。

2.  登录[数据传输控制台](https://dts.console.aliyun.com/)。

3.  在左侧导航栏，单击**数据同步**。

4.  在同步作业列表页面顶部，选择同步的目标实例所属地域。

    ![选择地域](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p50604.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。

6.  配置同步通道的源实例及目标实例信息。

    ![配置源和目标实例信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9620649951/p55482.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |无|同步作业名称|DTS会自动生成一个同步作业名称，建议配置具有业务意义的名称（无唯一性要求），便于后续识别。|
    |源实例信息|实例类型|选择**RDS实例**。|
    |实例地区|购买数据同步实例时选择的源实例地域信息，不可变更。|
    |实例ID|选择作为数据同步源的RDS实例ID。|
    |数据库账号|填入源RDS的数据库账号。 **说明：** 当源RDS实例的数据库类型为**MySQL 5.5**或**MySQL 5.6**时，无需配置**数据库账号**和**数据库密码**。 |
    |数据库密码|填入数据库账号对应的密码。|
    |连接方式|根据需求选择**非加密连接**或**SSL安全连接**。如果设置为**SSL安全连接**，您需要提前开启RDS实例的SSL加密功能，详情请参见[设置SSL加密](https://help.aliyun.com/document_detail/96120.html)。|
    |目标实例信息|实例类型|固定为**MaxCompute**，不可变更。|
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |Project|填入MaxCompute实例的**Project**，您可以在[MaxCompute工作空间列表](https://workbench.data.aliyun.com/consolenew#/projectlist)页面中查询。![MaxCompute工作空间列表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9620649951/p55484.png) |

7.  单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到RDS实例和MaxCompute实例的白名单中，用于保障DTS服务器能够正常连接源和目标实例。

8.  单击页面右下角的**下一步**，允许将MaxCompute中项目的下述权限授予给DTS同步账号，详情如下图所示。

    ![账号授权](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9620649951/p55487.png)

9.  配置同步策略和同步对象。

    ![配置同步策略和对象](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0720649951/p65864.png)

    |配置|说明|
    |:-|:-|
    |增量日志表分区定义|根据业务需求，选择分区名称。关于分区的相关介绍请参见[分区](https://help.aliyun.com/document_detail/27820.html)。|
    |同步初始化|同步初始化类型细分为：结构初始化、全量数据初始化。

此处同时勾选**结构初始化**和**全量数据初始化**，DTS会在增量数据同步之前，将源数据库中待同步对象的结构和存量数据同步到目标数据库。 |
    |目标已存在表的处理模式|    -   **预检查并报错拦截**：检查目标数据库中是否有同名的表。如果目标数据库中没有同名的表，则通过该检查项目；如果目标数据库中有同名的表，则在预检查阶段提示错误，数据同步作业不会被启动。

**说明：** 如果目标库中同名的表不方便删除或重命名，您可以[设置同步对象在目标实例中的名称](/cn.zh-CN/数据同步/同步作业管理/设置同步对象在目标实例中的名称.md)来避免表名冲突。

    -   **忽略报错并继续执行**：跳过目标数据库中是否有同名表的检查项。

**警告：** 选择为**忽略报错并继续执行**，可能导致数据不一致，给业务带来风险，例如：

        -   表结构一致的情况下，如果在目标库遇到与源库主键的值相同的记录，在初始化阶段会保留目标库中的该条记录；在增量同步阶段则会覆盖目标库的该条记录。
        -   表结构不一致的情况下，可能会导致无法初始化数据、只能同步部分列的数据或同步失败。 |
    |选择同步对象|在源库对象框中单击待同步的表，然后单击![向右小箭头](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p40698.png)将其移动至已选择对象框。

**说明：**

    -   同步对象支持选择的粒度仅为表，您可以从多个库中选择表作为同步对象。
    -   默认情况下，同步对象的名称保持不变。如果您需要在目标实例上名称不同，那么需要使用DTS提供的对象名映射功能，详情请参见[设置同步对象在目标实例中的名称](/cn.zh-CN/数据同步/同步作业管理/设置同步对象在目标实例中的名称.md)。 |
    |选择附加列规则|DTS在将数据同步到MaxCompute时，会在同步的目标表中添加一些附加列。如果附加列和目标表中已有的列出现名称冲突将会导致数据同步失败。您需要根据业务需求选择**是否启用新的附加列规则**为**是**或**否**。 **警告：** 在选择附加列规则前，您需要评估附加列和目标表中已有的列是否会出现名称冲突。关于附加列的规则和定义说明，请参见[表 1](/cn.zh-CN/数据同步/同步作业管理/修改数据同步的附加列规则.md)。 |

10. 上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：**

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![提示](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p47468.png)图标，查看失败详情。根据提示修复后，重新进行预检查。
11. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，同步作业将正式开始。

12. 等待同步作业的链路初始化完成，直至处于**同步中**状态。

    您可以在数据同步页面，查看数据同步作业的状态。

    ![查看同步作业状态](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1349459951/p41059.png)


## 增量日志表结构定义说明

DTS在将MySQL产生的增量数据同步至MaxCompute的增量日志表时，除了存储增量数据，还会存储一些元信息，示例如下。

![增量日志表结构](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0720649951/p55989.png)

**说明：** 示例中的`modifytime_year`、`modifytime_month`、`modifytime_day`、`modifytime_hour`、`modifytime_minute`为分区字段，是在[配置同步策略和同步对象](#step_pze_gpg_5gx)步骤中指定的。

结构定义说明

|字段|说明|
|:-|:-|
|record\_id|增量日志的记录ID，为该日志唯一标识。 **说明：**

-   ID的值唯一且递增。
-   如果增量日志的操作类型为UPDATE，那么增量更新会被拆分成两条记录（分别记录更新前和更新后的值），且record\_id的值相同。 |
|operation\_flag|操作类型，取值： -   I：INSERT操作。
-   D：DELETE操作。
-   U：UPDATE操作。 |
|utc\_timestamp|操作时间戳，即binlog的时间戳（UTC 时间）。|
|before\_flag|所有列的值是否为更新前的值，取值：Y或N。|
|after\_flag|所有列的值是否为更新后的值，取值：Y或N。|

## 关于before\_flag和after\_flag的补充说明

对于不同的操作类型，增量日志中的**before\_flag**和**after\_flag**定义如下：

-   INSERT

    当操作类型为INSERT时，所有列的值为新插入的记录值，即为更新后的值，所以before\_flag取值为N，after\_flag取值为Y，示例如下。

    ![INSERT操作示例](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0720649951/p55992.png)

-   UPDATE

    当操作类型为UPDATE时，DTS会将UPDATE操作拆为两条增量日志。这两条增量日志的record\_id、operation\_flag及utc\_timestamp对应的值相同。

    第一条增量日志记录了更新前的值，所以before\_flag取值为Y，after\_flag取值为N。第二条增量日志记录了更新后的值，所以before\_flag取值为N，after\_flag取值为Y，示例如下。

    ![UPDATE操作示例](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0720649951/p55993.png)

-   DELETE

    当操作类型为DELETE时，增量日志中所有的列值为被删除的值，即列值不变，所以before\_flag取值为Y，after\_flag取值为N，示例如下。

    ![DELETE操作示例](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0720649951/p55994.png)


## 全量数据合并示例

执行数据同步的操作后，DTS会在MaxCompute中分别创建该表的全量基线表和增量日志表。您可以通过MaxCompute的SQL命令，对这两个表执行合并操作，得到某个时间点的全量数据。

本案例以customer表为例（表结构如下），介绍操作流程。

![customer表结构](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0720649951/p56270.png)

1.  根据源库中待同步表的结构，在MaxCompute中创建用于存储合并结果的表。

    例如，需要获取customer表在`1565944878`时间点的全量数据。为方便业务识别，创建如下数据表：

    ```
    CREATE TABLE `customer_1565944878` (
        `id` bigint NULL,
        `register_time` datetime NULL,
        `address` string);
    ```

    **说明：**

    -   您可以在[MaxCompute的临时查询](~~142384~~)中，运行SQL命令。
    -   关于MaxCompute支持的数据类型与相关说明，请参见[数据类型](~~27821~~)。
2.  在MaxCompute中执行如下SQL命令，合并全量基线表和增量日志表，获取该表在某一时间点的全量数据。

    ```
    set odps.sql.allow.fullscan=true;
    insert overwrite table <result_storage_table>
    select <col1>,
           <col2>,
           <colN>
      from(
    select row_number() over(partition by t.<primary_key_column>
     order by record_id desc, after_flag desc) as row_number, record_id, operation_flag, after_flag, <col1>, <col2>, <colN>
      from(
    select incr.record_id, incr.operation_flag, incr.after_flag, incr.<col1>, incr.<col2>,incr.<colN>
      from <table_log> incr
     where utc_timestamp< <timestamp>
     union all
    select 0 as record_id, 'I' as operation_flag, 'Y' as after_flag, base.<col1>, base.<col2>,base.<colN>
      from <table_base> base) t) gt
    where record_num=1 
      and after_flag='Y'
    ```

    **说明：**

    -   <result\_storage\_table\>：存储全量merge结果集的表名。
    -   <col1\>/<col2\>/<colN\>：同步表中的列名。
    -   <primary\_key\_column\>：同步表中的主键列名。
    -   <table\_log\>：增量日志表名。
    -   <table\_base\>：全量基线表名。
    -   <timestamp\>：需要获取全量数据的时间点。
    合并数据表，获取customer表在`1565944878`时间点的全量数据，示例如下：

    ```
    set odps.sql.allow.fullscan=true;
    insert overwrite table customer_1565944878
    select id,
           register_time,
           address
      from(
    select row_number() over(partition by t.id
     order by record_id desc, after_flag desc) as row_number, record_id, operation_flag, after_flag, id, register_time, address
      from(
    select incr.record_id, incr.operation_flag, incr.after_flag, incr.id, incr.register_time, incr.address
      from customer_log incr
     where utc_timestamp< 1565944878
     union all
    select 0 as record_id, 'I' as operation_flag, 'Y' as after_flag, base.id, base.register_time, base.address
      from customer_base base) t) gt
     where gt.row_number= 1
       and gt.after_flag= 'Y';
    ```

3.  上述命令执行完成后，可在customer\_1565944878表中查询合并后的数据。

    ![查询merge后的数据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0720649951/p56256.png)


