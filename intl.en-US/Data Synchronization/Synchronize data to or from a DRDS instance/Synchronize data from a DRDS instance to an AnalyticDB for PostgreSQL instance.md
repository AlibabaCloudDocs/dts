# Synchronize data from a DRDS instance to an AnalyticDB for PostgreSQL instance

AnalyticDB for PostgreSQL \(previously known as HybridDB for PostgreSQL\) is a fast, easy-to-use, and cost-effective warehousing service that can process petabytes of data. This topic describes how to synchronize data from a DRDS instance to an AnalyticDB for PostgreSQL instance by using Data Transmission Service \(DTS\). The data synchronization feature is applicable to scenarios such as ad-hoc query and analysis, extract, transform, and load \(ETL\) operations, and data visualization.

-   A database is created in the DRDS instance based on one or more ApsaraDB RDS for MySQL instances.
-   The tables to be synchronized from the source database contain primary keys.
-   An AnalyticDB for PostgreSQL instance is created. For more information, see [Create an instance](https://www.alibabacloud.com/help/zh/doc-detail/50200.html).

## Precautions

-   DTS uses read and write resources of the source and destination databases during initial full data synchronization. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you synchronize data, evaluate the impact of data synchronization on the performance of the source and destination databases. We recommend that you synchronize data during off-peak hours. For example, you can synchronize data when the CPU utilization of the source and destination databases is less than 30%.
-   We recommend that you do not scale up or down the DRDS instance, migrate frequently-accessed tables, change shard keys, or perform DDL operations on objects during data synchronization. Otherwise, data synchronization may fail.
-   If you need to change the network type of a DRDS instance during data synchronization, you must [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createIndex) to modify the network connection information.
-   After a data synchronization task is started, new tables that are created in the source database can be synchronized. However, you must add the tables to the required objects. For more information see [Add an object to a data synchronization task](/intl.en-US/Data Synchronization/Synchronization task management/Add an object to a data synchronization task.md).

## Limits

-   You can select only tables as the objects to be synchronized.
-   DTS does not synchronize the following types of data: JSON, GEOMETRY, CURVE, SURFACE, MULTIPOINT, MULTILINESTRING, MULTIPOLYGON, and GEOMETRYCOLLECTION.

## SQL operations that can be synchronized

INSERT, UPDATE, and DELETE

## Term mappings

|DRDS|AnalyticDB for PostgreSQL|
|:---|:------------------------|
|Database|Schema|
|Table|Table|

## Procedure

1.  Purchase a data synchronization instance. For more information, see [Purchase a DTS instance]().

    **Note:** On the buy page, set Source Instance to **DRDS**, set Target Instance to **AnalyticDB for PostgreSQL**, and set Synchronization Topology to **One-Way Synchronization**.

2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

3.  In the left-side navigation pane, click **Data Synchronization**.

4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column.

6.  Configure the source and destination instances.

    ![Configure the source and destination instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0610398951/p65398.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|The value of this parameter is set to **DRDS** and cannot be changed.|
    |Instance Region|The region of the source instance. The region is the same as the source region that you selected on the buy page. You cannot change the value of this parameter.|
    |DRDS Instance ID|Select the ID of the DRDS instance.|
    |Destination Instance Details|Instance Type|The value of this parameter is set to **AnalyticDB for PostgreSQL** and cannot be changed.|
    |Instance Region|The region of the destination instance. The region is the same as the destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |Instance ID|Select the ID of the AnalyticDB for PostgreSQL instance.|
    |Database Name|Enter the name of the destination database in the AnalyticDB for PostgreSQL instance.|
    |Database Account|Enter the database account of the destination AnalyticDB for PostgreSQL instance.|
    |Database Password|Enter the password of the destination database account.|

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelists of the source DRDS instance and the destination AnalyticDB for PostgreSQL instance. This ensures that DTS servers can connect to the source and destination instances.

8.  Select the synchronization policy and the objects to be synchronized.

    ![Configure the synchronization policy and objects](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0610398951/p65407.png)

    |Setting|Parameter|Description|
    |:------|:--------|:----------|
    |Select the synchronization policy|Initial Synchronization|You must select both **Initial Schema Synchronization** and **Initial Full Data Synchronization** in most cases. After the precheck, DTS synchronizes the schemas and data of the required objects from the source instance to the destination instance. The schemas and data are the basis for subsequent incremental synchronization.|
    |Processing Mode In Existed Target Table|    -   **Clear Target Table**

Skips the **Schema Name Conflict** item during the precheck. Clears the data in the destination table before initial full data synchronization. If you want to synchronize your business data after testing the data synchronization task, you can select this mode.

    -   **Ignore**

Skips the **Schema Name Conflict** item during the precheck. Adds data to the existing data during initial full data synchronization. If you want to synchronize data from multiple tables to one table, you can select this mode. |
    |Synchronization Type|Select the types of operations that you want to synchronize based on your business requirements.

    -   **Insert**
    -   **Update**
    -   **Delete** |
    |Select the objects to be synchronized|N/A|Select tables from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the tables to the Selected section.

**Note:**

    -   You can select only tables as the objects to be synchronized.
    -   You can use the object name mapping feature to change the names of the columns that are synchronized to the destination database. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md). |

9.  In the lower-right corner of the page, click **Next**.

10. Specify the primary key column and distribution column of the table that you want to synchronize to the AnalyticDB for PostgreSQL instance.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4703019951/p65402.png)

    **Note:** The page in this step appears only if you select **Initial Schema Synchronization** in [Step 8](#step_179_1cn_6zy). For more information about primary key columns and distribution columns, see [Define constraints](~~118150~~) and [Define table distribution](~~120143~~).

11. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, a precheck is performed. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
12. Close the Precheck dialog box after the following message is displayed: **The precheck is passed**.

13. Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    On the Synchronization Tasks page, view the status of the data synchronization task.

    ![View the status of a data synchronization task.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5130359951/p41059.png)


