# 从RDS SQL Server同步至云原生数据仓库AnalyticDB PostgreSQL

数据传输服务DTS（Data Transmission Service）支持将SQL Server同步至云原生数据仓库AnalyticDB PostgreSQL，帮助您轻松实现数据的流转，集中分析企业数据。

-   [创建RDS SQL Server实例](/cn.zh-CN/RDS SQL Server 数据库/快速入门/创建RDS SQL Server实例.md)（SQL Server 2012、2016或2017版本）。
-   [创建云原生数据仓库AnalyticDB PostgreSQL实例](/cn.zh-CN/快速入门/创建实例.md)。
-   RDS SQL Server实例中待同步的表需具备主键。
-   云原生数据仓库AnalyticDB PostgreSQL实例中同步的目标表需具备主键或唯一索引。

## 注意事项

-   DTS在执行全量数据迁移时将占用源库和目标库一定的读写资源，可能会导致数据库的负载上升，在数据库性能较差、规格较低或业务量较大的情况下（例如源库有大量慢SQL、存在无主键表或目标库存在死锁等），可能会加重数据库压力，甚至导致数据库服务不可用。因此您需要在执行数据迁移前评估源库和目标库的性能，同时建议您在业务低峰期执行数据迁移（例如源库和目标库的CPU负载在30%以下）。
-   为保障数据同步延迟显示的准确性，DTS会在源库中新增一张心跳表（名称为`dts_log_heart_beat`）。
-   此场景中，DTS支持初始化的结构为Schema、Table、View、Function和Procedure。

    **警告：** 由于此场景属于异构数据库间的数据同步，数据类型无法一一对应，请谨慎评估数据类型的映射关系对业务的影响，详情请参见[结构初始化涉及的数据类型映射关系](/cn.zh-CN/数据同步/结构初始化涉及的数据类型映射关系.md)。

-   不支持同步数据类型为TIMESTAMP、CURSOR、ROWVERSION、HIERACHYID、SQL\_VARIANT、SPATIAL GEOMETRY、SPATIAL GEOGRAPHY、TABLE的数据。

## 支持同步的SQL操作

-   DDL操作：ADD COLUMN
-   DML操作：INSERT、UPDATE、DELETE

## 数据库账号的权限要求

|数据库|所需权限|授权方法|
|:--|:---|----|
|RDS SQL Server实例|待同步数据库的所有者权限。|[修改账号权限](https://help.aliyun.com/document_detail/95692.html)|
|云原生数据仓库AnalyticDB PostgreSQL实例|-   LOGIN权限。
-   目标表的SELECT、CREATE、INSERT、UPDATE、DELETE权限。
-   目标库的CONNECT、CREATE权限。
-   目标Schema的CREATE权限。
-   Copy权限（基于内存batch copy）。

**说明：** 您也可以使用云原生数据仓库AnalyticDB PostgreSQL实例的初始账号。

|[用户权限管理](https://help.aliyun.com/document_detail/35430.html)|

## 操作步骤

1.  购买数据同步作业，详情请参见[购买流程](/cn.zh-CN/快速入门/购买流程.md)。

    **说明：** 购买时，选择源实例为**SQLServer**，目标实例为**AnalyticDB for PostgreSQL**，并选择同步拓扑为**单向同步**。

2.  登录[数据传输控制台](https://dts.console.aliyun.com/)。

3.  在左侧导航栏，单击**数据同步**。

4.  在同步作业列表页面顶部，选择同步的目标实例所属地域。

    ![选择地域](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p50604.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。

6.  配置同步作业的源实例及目标实例信息。

    ![配置源库和目标库信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0580190061/p93637.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |无|同步作业名称|DTS会自动生成一个同步作业名称，建议配置具有业务意义的名称（无唯一性要求），便于后续识别。|
    |源实例信息|实例类型|选择**RDS实例**。|
    |实例地区|购买数据同步实例时选择的源实例地域信息，不可变更。|
    |实例ID|选择源RDS SQL Server实例ID。|
    |数据库账号|填入RDS SQL Server的数据库账号。权限要求请参见[数据库账号的权限要求](#section_im3_3vb_7jk) 。|
    |数据库密码|填入该数据库账号的密码。|
    |连接方式|根据需求选择**非加密连接**或**SSL安全连接**。如果设置为**SSL安全连接**，您需要提前开启RDS实例的SSL加密功能，详情请参见[设置SSL加密](/cn.zh-CN/RDS SQL Server 数据库/数据安全/加密/设置SSL加密.md)。|
    |目标实例信息|实例类型|固定为**AnalyticDB for PostgreSQL**，无需设置。|
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |实例ID|选择目标云原生数据仓库AnalyticDB PostgreSQL实例ID。|
    |数据库名称|填入同步目标表所属的数据库名称。|
    |数据库账号|填入云原生数据仓库AnalyticDB PostgreSQL的数据库账号。权限要求请参见[数据库账号的权限要求](#section_im3_3vb_7jk) 。|
    |数据库密码|填入该数据库账号对应的密码。|

7.  单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到RDS SQL Server和云原生数据仓库AnalyticDB PostgreSQL的白名单中，用于保障DTS服务器能够正常连接源和目标实例。

8.  配置同步策略和同步对象。

    ![SQL Sever配置同步策略及对象](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1330649951/p93643.png)

    |配置|说明|
    |:-|:-|
    |同步初始化|默认选中**结构初始化**、**全量数据初始化**和**增量数据初始化**。预检查完成后，DTS会将源实例中待同步对象的结构和存量数据同步至目标在目标库，作为后续增量同步数据的基线数据。|
    |目标已存在表的处理模式|    -   **预检查并报错拦截**：检查目标数据库中是否有同名的表。如果目标数据库中没有同名的表，则通过该检查项目；如果目标数据库中有同名的表，则在预检查阶段提示错误，数据同步作业不会被启动。

**说明：** 如果目标库中同名的表不能删除或重命名，您可以更改该表在目标库中的名称，详情请参见[设置同步对象在目标实例中的名称](/cn.zh-CN/数据同步/同步作业管理/设置同步对象在目标实例中的名称.md)。

    -   **忽略报错并继续执行**：跳过目标数据库中是否有同名表的检查项。

**警告：** 选择为**忽略报错并继续执行**，可能导致数据不一致，给业务带来风险，例如：

        -   表结构一致的情况下，在目标库遇到与源库主键的值相同的记录，则会保留目标集群中的该条记录，即源库中的该条记录不会同步至目标数据库中。
        -   表结构不一致的情况下，可能会导致无法初始化数据、只能同步部分列的数据或同步失败。 |
    |多表归并|    -   选择为**是**：通常在OLTP场景中，为提高业务表响应速度，通常会做分库分表处理。而在云原生数据仓库AnalyticDB PostgreSQL中单个数据表可存储海量数据，使用单表查询更加便捷。此类场景中，您可以借助DTS的多表归并功能将源库中多个表结构相同的表（即各分表）同步至云原生数据仓库AnalyticDB PostgreSQL中的同一个表中。

**说明：**

        -   选择源库的多个表后，您需要通过对象名映射功能，将其改为云原生数据仓库AnalyticDB PostgreSQL中的同一个表名。关于对象名映射功能的介绍，请参见[设置同步对象在目标实例中的名称](/cn.zh-CN/数据同步/同步作业管理/设置同步对象在目标实例中的名称.md)。
        -   您需要在云原生数据仓库AnalyticDB PostgreSQL的同步目标表中增加`__dts_data_source`列（类型为text）来存储数据来源。DTS将以`<dts数据同步实例ID>:<源数据库名>.<源Schema名>.<源表名>`的格式写入列值用于区分表的来源，例如`dts********:dtstestdata.testschema.customer1`。
        -   多表归并功能基于任务级别，即不支持基于表级别执行多表归并。如果需要让部分表执行多表归并，另一部分不执行多表归并，您需要创建两个数据同步作业。
    -   选择为**否**：默认选项。 |
    |同步操作类型|根据业务选中需要同步的操作类型，默认情况下都处于选中状态。|
    |选择同步对象|在源库对象框中单击待迁移的对象，然后单击![向右小箭头](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p40698.png)图标将其移动至已选择对象框。

同步对象的选择粒度为库、表。

**说明：**

    -   默认情况下，同步对象的名称保持不变。如果您需要同步对象在目标实例上名称不同，请使用对象名映射功能，详情请参见[设置同步对象在目标实例中的名称](/cn.zh-CN/数据同步/同步作业管理/设置同步对象在目标实例中的名称.md)。
    -   如果配置**多表归并**为**是**，在选择源库的多个表后，您需要通过对象名映射功能，将其改为云原生数据仓库AnalyticDB PostgreSQL中的同一个表名。 |
    |为目标对象添加引号|选择是否需要为目标对象名添加引号。如果选择为**是**，且存在下述情况，DTS在结构初始化阶段和增量数据迁移阶段会为目标对象添加单引号或双引号：     -   源库所属的业务环境对大小写敏感且大小写混用。
    -   源表名不是以字母开头，且包含字母、数字或特殊字符以外的字符。

**说明：** 特殊字符仅支持下划线（\_），井号（\#）和美元符号（$）。

    -   待迁移的Schema、表或列名称是目标库的关键字、保留字或非法字符。
**说明：** 如果选择添加引号，在数据同步完成后，您需使用带引号的目标对象名进行查询。 |

9.  设置待同步的表在云原生数据仓库AnalyticDB PostgreSQL中表类型、主键列和分布键信息。

    ![设置表类型 主键列 分布列](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1330649951/p93638.png)

    **说明：** 关于主键列和分布键的详细说明，请参见[表的约束定义](https://help.aliyun.com/document_detail/118150.html#title-8zk-q8o-q3l)和[表分布键定义](https://help.aliyun.com/document_detail/120143.html)。

10. 上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：**

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![提示](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p47468.png)图标，查看失败详情。根据提示修复后，重新进行预检查。
11. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，同步作业将正式开始。

12. 等待同步作业的链路初始化完成，直至处于**同步中**状态。

    您可以在数据同步页面，查看数据同步作业的状态。

    ![查看同步作业状态](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1349459951/p41059.png)


## 常见问题

Q：如何在云原生数据仓库AnalyticDB PostgreSQL找到同步的目标表？

A：DTS的结构初始化会遵循源库的结构将其同步至目标库。本案例中，您可以在目标实例的`dtstestdata`数据库的`dbo` Schema中，找到`customer`表和`Student`表，如下图所示。

![ADB for PG查看目标表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2330649951/p130596.png)

