# Synchronize data from an ApsaraDB RDS for MySQL instance to an AnalyticDB for PostgreSQL instance

This topic describes how to synchronize data from an ApsaraDB RDS for MySQL instance to an AnalyticDB for PostgreSQL instance by using Data Transmission Service \(DTS\). The data synchronization feature provided by DTS allows you to transfer and analyze data with ease.

-   The tables that you want to synchronize from the ApsaraDB RDS for MySQL instance contain primary keys.
-   An AnalyticDB for PostgreSQL instance is created. For more information, see [Create an instance](https://www.alibabacloud.com/help/zh/doc-detail/50200.htm).

## Precautions

DTS uses read and write resources of the source and destination databases during initial full data synchronization. This may increase the load of the database server. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you synchronize data, evaluate the impact of data synchronization on the performance of the source and destination databases. We recommend that you synchronize data during off-peak hours. For example, you can synchronize data when the CPU utilization of the source and destination databases is less than 30%.

## Limits

-   You can select only tables as the objects to be synchronized.
-   You cannot synchronize the following types of data: BIT, VARBIT, GEOMETRY, ARRAY, UUID, TSQUERY, TSVECTOR, and TXID\_SNAPSHOT.
-   Prefix indexes cannot be synchronized. If the source database contains prefix indexes, data may fail to be synchronized.
-   We recommend that you do not use gh-ost or pt-online-schema-change to perform data definition language \(DDL\) operations on objects during data synchronization. Otherwise, data synchronization may fail.

## SQL operations that can be synchronized

-   Data manipulation language \(DML\) operations: INSERT, UPDATE, and DELETE
-   DDL operations: ADD COLUMN and RENAME COLUMN

    **Note:** The CREATE TABLE operation is not supported. To synchronize data from a new table, you must add the table to the selected objects. For more information, see [Add an object to a data synchronization task](/intl.en-US/Data Synchronization/Synchronization task management/Add an object to a data synchronization task.md).


## Supported synchronization topologies

-   One-way one-to-one synchronization
-   One-way one-to-many synchronization
-   One-way many-to-one synchronization

## Term mappings

|MySQL|AnalyticDB for PostgreSQL|
|:----|:------------------------|
|Database|Schema|
|Table|Table|

## Procedure

1.  Purchase a data synchronization instance. For more information, see [Purchase procedure]().

    **Note:** On the buy page, set Source Instance to **MySQL**, set Target Instance to **AnalyticDB for PostgreSQL**, and set Synchronization Topology to **One-Way Synchronization**.

2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

3.  In the left-side navigation pane, click **Data Synchronization**.

4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column.

6.  Configure the source and destination instances.

    ![Configure the source and destination instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8900398951/p45688.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|Select **RDS Instance**.|
    |Instance Region|The region of the source instance. The region is the same as the source region that you selected on the buy page. You cannot change the value of this parameter.|
    |Instance ID|Select the ID of the source RDS instance.|
    |Database Account|Enter the database account of the ApsaraDB RDS for MySQL instance. **Note:** If the database engine of the source RDS instance is **MySQL 5.5** or **MySQL 5.6**, you do not need to configure the **database account** and **database password**. |
    |Database Password|Enter the password of the source database account.|
    |Encryption|Select **Non-encrypted** or **SSL-encrypted**. If you want to select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before you configure the data migration task. For more information, see [Configure SSL encryption on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96120.htm). **Note:** The **Encryption** parameter is available only for regions in mainland China and the China \(Hong Kong\) region. |
    |Destination Instance Details|Instance Type|The value of this parameter is set to **AnalyticDB for PostgreSQL** and cannot be changed.|
    |Instance Region|The region of the destination instance. The region is the same as the destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |Instance ID|Select the ID of the destination AnalyticDB for PostgreSQL instance.|
    |Database Name|Enter the name of the destination database.|
    |Database Account|Enter the AnalyticDB for PostgreSQL of the **** instance. For more information, see [t16843.md\#](/intl.en-US/Quick Start/Configure an account.md). **Note:** You can also enter an account that has the RDS\_SUPERUSER permission. For more information, see [Manage users and permissions](/intl.en-US/Beginner Developer Guide/Manage users and permissions.md). |
    |Database Password|Enter the password of the destination database account.|

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelists of the ApsaraDB RDS for MySQL instance and the AnalyticDB for PostgreSQL instance. This ensures that DTS servers can connect to the source RDS instance.

8.  Select the synchronization policy and the objects to be synchronized.

    ![Synchronize data from MySQL to AnalyticDB for PostgreSQL](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4703019951/p96654.png)

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
    -   **Delete**
    -   **AlterTable** |
    |Select the objects to be synchronized|N/A|Select tables from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the tables to the Selected section.

**Note:**

    -   You can select only tables as the objects to be synchronized.
    -   You can use the object name mapping feature to change the names of the columns that are synchronized to the destination database. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md). |

9.  Specify the primary key column and distribution column of the table that you want to synchronize to the AnalyticDB for PostgreSQL instance.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4703019951/p65402.png)

    **Note:** The page in this step appears only if you select **Initial Schema Synchronization**. For more information about primary key columns and distribution columns, see [Define constraints](https://www.alibabacloud.com/help/zh/doc-detail/118150.htm#title-8zk-q8o-q3l) and [Define table distribution](https://www.alibabacloud.com/help/zh/doc-detail/120143.htm).

10. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, a precheck is performed. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run a precheck again.
11. Close the Precheck dialog box after the following message is displayed: **The precheck is passed.** Then, the data synchronization task starts.

12. Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    You can view the status of the data synchronization task on the Synchronization Tasks page.

    ![View the status of a data synchronization task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5130359951/p41059.png)


