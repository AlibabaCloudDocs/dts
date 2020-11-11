# Synchronize data from a PolarDB for MySQL cluster to an AnalyticDB for MySQL cluster

AnalyticDB for MySQL is a real-time online analytical processing \(RT-OLAP\) service that is developed by Alibaba Cloud for online data analysis with high concurrency. AnalyticDB for MySQL can analyze petabytes of data from multiple dimensions at millisecond-level timing to provide you with data-driven insights into your business. This topic describes how to synchronize data from a PolarDB for MySQL cluster to an AnalyticDB for MySQL cluster by using Data Transmission Service \(DTS\). After you synchronize data, you can use AnalyticDB for MySQL to build internal business intelligence \(BI\) systems, interactive query systems, and real-time reporting systems.

-   An AnalyticDB for MySQL cluster is created. For more information, see [Create an AnalyticDB for MySQL cluster](https://www.alibabacloud.com/help/zh/doc-detail/122234.htm).
-   The destination AnalyticDB for MySQL cluster has sufficient storage space.
-   The binary logging feature is enabled for the PolarDB for MySQL cluster. For more information, see [Enable binary logging](https://www.alibabacloud.com/help/zh/doc-detail/113546.htm)

## 注意事项

-   DTS uses read and write resources of the source and destination databases during initial full data synchronization. This may increase the database load. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before synchronizing data, you must evaluate the performance of the source and destination databases. We recommend that you synchronize data during off-peak hours. For example, you can synchronize data when the CPU usage of the source and destination databases is less than 30%.
-   请勿在数据同步时，对源库的同步对象使用gh-ost或pt-online-schema-change等类似工具执行在线DDL变更，否则会导致同步失败。
-   由于AnalyticDB for MySQL本身的使用限制，当AnalyticDB for MySQL集群中的节点磁盘空间使用量超过80%，该集群将被锁定。请提前根据待同步的对象预估所需空间，确保目标集群具备充足的存储空间。
-   暂不支持同步前缀索引，如果源库存在前缀索引可能导致数据同步失败。

## SQL operations that can be synchronized

-   DDL操作：CREATE TABLE、DROP TABLE、RENAME TABLE、TRUNCATE TABLE、ADD COLUMN、DROP COLUMN
-   DML操作：INSERT、UPDATE、DELETE

**Note:** If the data type of a field in the source table is changed during data synchronization, an error message is generated and the data synchronization task stops. You can [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm#/ticket/createIndex) or manually troubleshoot the issue. For more information, see [修复因变更字段类型导致的同步失败](/intl.en-US/Data Synchronization/Synchronize data from MySQL databases to other databases/Synchronize data from an ApsaraDB RDS for MySQL instance to an AnalyticDB for MySQL
         cluster.md).

## Permissions required for database accounts

|Database|Required permission|
|--------|-------------------|
|PolarDB MySQL|The read permission on the objects to be synchronized|
|AnalyticDB for MySQL|The read and write permissions on the objects to be synchronized|

For more information about how to create and authorize a database account, see [Create a PolarDB for MySQL cluster](https://www.alibabacloud.com/help/zh/doc-detail/68508.htm) and [Create an AnalyticDB for MySQL database account](https://www.alibabacloud.com/help/zh/doc-detail/122280.htm).

## Data type mappings

For more information, see [Data type mappings for initial schema synchronization](/intl.en-US/Data Synchronization/Data type mappings for initial schema synchronization.md).

## Procedure

1.  [Purchase a data synchronization instance]().

    **Note:** On the buy page, set Source Instance to **PolarDB**, set Target Instance to **AnalyticDB for MySQL**, and set Synchronization Topology to **One-Way Synchronization**.

2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

3.  In the left-side navigation pane, click **Data Synchronization**.

4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column.

6.  Configure the source and destination instances.

    ![Configure the source and destination instances](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0703019951/p58269.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|The value of this parameter is set to **PolarDB Instance** and cannot be changed.|
    |Instance Region|The region of the source cluster. The region is the same as the source region that you selected on the buy page. You cannot change the value of this parameter.|
    |PolarDB Instance ID|Select the ID of the source PolarDB cluster.|
    |Database Account|Enter the database account of the source PolarDB cluster. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_77r_dbi_ez7).|
    |Database Password|Enter the password of the source database account.|
    |Destination Instance Details|Instance Type|The value of this parameter is set to **ADS** and cannot be changed.|
    |Instance Region|The region of the destination cluster. The region is the same as the destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |Version|Select **3.0**.|
    |Database|Select the ID of the destination AnalyticDB for MySQL cluster.|
    |Database Account|Enter the database account of the AnalyticDB for MySQL cluster. For more information about permissions required for the account, see [Permissions required for database accounts](#section_77r_dbi_ez7).|
    |Database Password|Enter the password of the destination database account.|

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelists of the PolarDB for MySQL cluster and the AnalyticDB for MySQL cluster. This ensures that DTS servers can connect to the source and destination clusters.

8.  配置同步策略及对象信息。

    ![配置同步策略和对象](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4130359951/p55267.png)

    |配置|说明|
    |:-|:-|
    |同步初始化|默认情况下，您需要同时选中**结构初始化**和**全量数据初始化**。预检查完成后，DTS会将源实例中待同步对象的结构及数据在目标集群中初始化，作为后续增量同步数据的基线数据。|
    |目标已存在表的处理模式|    -   **预检查并报错拦截**：检查目标数据库中是否有同名的表。如果目标数据库中没有同名的表，则通过该检查项目；如果目标数据库中有同名的表，则在预检查阶段提示错误，数据同步作业不会被启动。

**Note:** 如果目标库中同名的表不方便删除或重命名，您可以更改该表在目标库中的名称，详情请参见[Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md)。

    -   **忽略报错并继续执行**：跳过目标数据库中是否有同名表的检查项。

**Warning:** 选择为**忽略报错并继续执行**，可能导致数据不一致，给业务带来风险，例如：

        -   表结构一致的情况下，在目标库遇到与源库主键的值相同的记录，则会保留目标集群中的该条记录，即源库中的该条记录不会同步至目标数据库中。
        -   表结构不一致的情况下，可能会导致无法初始化数据、只能同步部分列的数据或同步失败。 |
    |多表归并|    -   选择为**是**：DTS将在每个表中增加`__dts_data_source`列来存储数据来源，且不再支持DDL同步。
    -   选择为**否**：默认选项，支持DDL同步。
**Note:** 多表归并功能基于任务级别，即不支持基于表级别执行多表归并。如果需要让部分表执行多表归并，另一部分不执行多表归并，您可以创建两个数据同步作业。 |
    |同步操作类型|根据业务选中需要同步的操作类型，默认情况下都处于选中状态。 **Note:** 目前仅支持INSERT、UPDATE、DELETE、ADD COLUMN。 |
    |选择同步对象|在源库对象框中单击待同步的对象，然后单击![向右小箭头](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3457359951/p40698.png)图标将其移动至已选择对象框。

同步对象的选择粒度为库、表。

**Note:**

    -   如果选择整个库作为同步对象，那么该库中所有对象的结构变更操作会同步至目标库。
    -   如果选择某个表作为同步对象，那么只有这个表的ADD COLUMN操作会同步至目标库。
    -   默认情况下，同步对象的名称保持不变。如果您需要同步对象在目标集群上名称不同，请使用对象名映射功能，详情请参见[Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md)。 |

9.  In the lower-right corner of the page, click **Next**.

10. 设置待同步的表在目标库中类型。

    ![设置表类型](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4130359951/p55270.png)

    **Note:** 选择了**结构初始化**后，您需要定义待同步的表在AnalyticDB for MySQL中的**类型**、主**键列**、**分区列**等信息，详情请参见[CREATE TABLE操作手册](https://www.alibabacloud.com/help/zh/doc-detail/123333.htm)。

11. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, a precheck is performed. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
12. Close the Precheck dialog box after the following message is displayed: **The precheck is passed**.

13. Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    On the Synchronization Tasks page, view the status of the data synchronization task.

    ![View the status of a data synchronization task.](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5130359951/p41059.png)


