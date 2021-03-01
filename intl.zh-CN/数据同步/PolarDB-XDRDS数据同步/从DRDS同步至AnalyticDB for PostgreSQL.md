# 从DRDS同步至AnalyticDB for PostgreSQL

分析型数据库PostgreSQL版（原HybridDB for PostgreSQL）为您提供简单、快速、经济高效的PB级云端数据仓库解决方案。通过数据传输服务DTS（Data Transmission Service），您可以将DRDS同步至AnalyticDB for PostgreSQL，帮助您快速实现对海量数据的即席查询分析、ETL处理和可视化探索。

-   DRDS中的数据库须基于RDS MySQL创建。
-   源库中待同步的表必须具备主键。
-   已创建AnalyticDB for PostgreSQL实例，详情请参见[创建AnalyticDB for PostgreSQL实例](https://www.alibabacloud.com/help/zh/doc-detail/50200.html)。

## 注意事项

-   DTS在执行全量数据初始化时将占用源库和目标库一定的读写资源，可能会导致数据库的负载上升，在数据库性能较差、规格较低或业务量较大的情况下（例如源库有大量慢SQL、存在无主键表或目标库存在死锁等），可能会加重数据库压力，甚至导致数据库服务不可用。因此您需要在执行数据同步前评估源库和目标库的性能，同时建议您在业务低峰期执行数据同步（例如源库和目标库的CPU负载在30%以下）。
-   数据同步期间，请勿对DRDS执行扩容、缩容、迁移热点表、变更拆分键和变更DDL等操作，否则将导致数据同步失败。
-   如果需要在数据同步期间切换DRDS的网络类型，在您执行完网络类型切换操作后，请[提交工单](https://workorder-intl.console.aliyun.com/#/ticket/createIndex)调整同步链路的网络连接信息。
-   在成功启动数据同步作业后，如果在源库中新增了一个表并需要将其作为同步对象，您需要为数据同步作业[新增同步对象](/intl.zh-CN/数据同步/同步作业管理/新增同步对象.md)。

## 功能限制

-   仅支持表级别的数据同步。
-   不支持同步JSON、GEOMETRY、CURVE、SURFACE、MULTIPOINT、MULTILINESTRING、MULTIPOLYGON、GEOMETRYCOLLECTION类型的数据。

## 支持同步的SQL操作

INSERT、UPDATE、DELETE。

## 术语/概念对应关系

|DRDS|AnalyticDB for PostgreSQL|
|:---|:------------------------|
|Database|Schema|
|Table|Table|

## 操作步骤

1.  购买数据同步作业，详情请参见[购买流程]()。

    **说明：** 购买时，选择源实例为**PolarDB-X（原DRDS升级版）**、目标实例为**AnalyticDB for PostgreSQL**，并选择同步拓扑为**单向同步**。

2.  登录[数据传输控制台](https://dts-intl.console.aliyun.com/)。

3.  在左侧导航栏，单击**数据同步**。

4.  在同步作业列表页面顶部，选择同步的目标实例所属地域。

    ![选择地域](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p50604.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。

6.  配置同步作业的源实例及目标实例信息。

    ![配置源和目标实例信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8230649951/p65398.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |无|同步作业名称|DTS会自动生成一个同步作业名称，建议配置具有业务意义的名称（无唯一性要求），便于后续识别。|
    |源实例信息|实例类型|固定为**DRDS实例**，不可变更。|
    |实例地区|购买数据同步作业时选择的源实例地域信息，不可变更。|
    |DRDS实例ID|选择DRDS的实例ID。|
    |目标实例信息|实例类型|固定为**AnalyticDB for PostgreSQL**，不可变更。|
    |实例地区|购买数据同步作业时选择的目标实例地域信息，不可变更。|
    |实例ID|选择AnalyticDB for PostgreSQL实例ID。|
    |数据库名称|填入目标AnalyticDB for PostgreSQL实例中，同步目标表所属的数据库名称。|
    |数据库账号|填入AnalyticDB for PostgreSQL的数据库账号。|
    |数据库密码|填入该数据库账号对应的密码。|

7.  单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到DRDS和AnalyticDB for PostgreSQL的白名单中，用于保障DTS服务器能够正常连接源和目标实例。

8.  配置同步策略及对象信息。

    ![配置同步策略和同步对象](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8230649951/p65407.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |同步策略配置|同步初始化|默认情况下，您需要同时勾选**结构初始化**和**全量数据初始化**。预检查完成后，DTS会将源实例中待同步对象的结构及数据在目标实例中初始化，作为后续增量同步数据的基线数据。|
    |目标已存在表的处理模式|    -   **清空目标表的数据**

在预检查阶段跳过**目标表是否为空**的检查项目。全量初始化之前将目标表的数据清空。适用于完成同步作业测试后的正式同步场景。

    -   **忽略报错并继续执行**

在预检查阶段跳过**目标表是否为空**的检查项目。全量初始化时直接追加同步数据。适用于多张表同步到一张表的汇总同步场景。 |
    |同步操作类型|根据业务需求选择需要同步的操作类型：

    -   **Insert**
    -   **Update**
    -   **Delete** |
    |选择同步对象|无|在源库对象框中单击待同步的表，然后单击![向右小箭头](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p40698.png)将其移动至已选择对象框。

**说明：**

    -   同步对象的选择粒度为表。
    -   如果需要目标表中列信息与源表不同，则需要使用DTS的字段映射功能，详情请参见[设置同步对象在目标实例中的名称](/intl.zh-CN/数据同步/同步作业管理/设置同步对象在目标实例中的名称.md)。 |

9.  上述配置完成后，单击页面右下角的**下一步**。

10. 设置待同步的表在AnalyticDB for PostgreSQL中的主键列和分布列信息。

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4330649951/p65402.png)

    **说明：** 在[第8步](#step_179_1cn_6zy)中选择了**结构初始化**才会出现该页面。关于主键列和分布列的详细说明，请参见[表的约束定义](~~118150~~)和[表分布键定义](~~120143~~)。

11. 上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：**

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![提示](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p47468.png)图标，查看失败详情。根据提示修复后，重新进行预检查。
12. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，同步作业将正式开始。

13. 等待同步作业的链路初始化完成，直至处于**同步中**状态。

    您可以在数据同步页面，查看数据同步作业的状态。

    ![查看同步作业状态](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1349459951/p41059.png)


