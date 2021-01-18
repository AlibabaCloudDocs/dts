# Synchronize data between ApsaraDB RDS for MySQL instances that belong to different Alibaba Cloud accounts

This topic describes how to synchronize data between ApsaraDB RDS for MySQL instances that belong to different Alibaba Cloud accounts by using Data Transmission Service \(DTS\).

## Prerequisites

-   The source and destination ApsaraDB RDS for MySQL instances are created. For more information, see [Create an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/26117.htm).
-   The database type of the source and destination RDS instances is MySQL.
-   The source and destination ApsaraDB RDS for MySQL instances have internal endpoints.

## Precautions

-   DTS uses read and write resources of the source and destination databases during initial full data synchronization. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you synchronize data, evaluate the impact of data synchronization on the performance of the source and destination databases. We recommend that you synchronize data during off-peak hours. For example, you can synchronize data when the CPU utilization of the source and destination databases is less than 30%.
-   If you have selected one or more tables \(not a database\) for synchronization, do not use gh-ost or pt-online-schema-change to modify the tables during data synchronization. Otherwise, data synchronization may fail.

    **Note:** To avoid synchronization failure, you can use Data Management \(DMS\) to perform online DDL schema changes during data synchronization. For more information, see [Change the table schema without locking](https://www.alibabacloud.com/help/doc-detail/98373.htm).

-   The tables to be migrated in the source database must have PRIMARY KEY or UNIQUE constraints and all fields must be unique. Otherwise, the destination database may contain duplicate data records.
-   During initial full data synchronization, concurrent INSERT operations cause fragmentation in the tables of the destination instance. After initial full data synchronization, the tablespace of the destination instance is larger than that of the source instance.

## Supported synchronization typologies

-   One-way one-to-one synchronization
-   One-way one-to-many synchronization
-   One-way cascade synchronization
-   One-way many-to-one synchronization

For more information about synchronization topologies, see [Synchronization topologies](/intl.en-US/Data Synchronization/Synchronization topologies.md).

## SQL operations that can be synchronized

|Operation type|SQL statements|
|--------------|--------------|
|DML|INSERT, UPDATE, DELETE, and REPLACE|
|DDL|-   ALTER TABLE and ALTER VIEW
-   CREATE FUNCTION, CREATE INDEX, CREATE PROCEDURE, CREATE TABLE, and CREATE VIEW
-   DROP INDEX and DROP TABLE
-   RENAME TABLE
-   TRUNCATE TABLE |

## Limits

-   Incompatibility with triggers

    If you select a database as the object and the database contains a trigger that updates a table, data inconsistency may occur. For more information about how to solve this issue, see [Configure a data synchronization task for a source database that contains a trigger](/intl.en-US/Best Practices/Configure a data synchronization task for a source database that contains a trigger.md).

-   Limits on RENAME TABLE operations

    RENAME TABLE operations may cause data inconsistency during synchronization. For example, if only Table A needs to be synchronized and it is renamed Table B, Table B cannot be synchronized to the destination database. To prevent this situation, you can select the entire database where Table A is located as the object when you configure the data synchronization task.


## Before you begin

Set the Alibaba Cloud account that owns the destination RDS instance as a trusted account. This allows DTS to access the cloud resources of the Alibaba Cloud account that owns the source RDS instance. For more information, see [Configure RAM authorization for cross-account data migration and synchronization](/intl.en-US/RAM-based Access Control/Configure RAM authorization for cross-account data migration and synchronization.md).

**Note:** To authorize the Alibaba Cloud account that owns the destination instance, you must log on to the RAM console with the Alibaba Cloud account that owns the source instance. Then, you can create a data migration task or data synchronization task by using the Alibaba Cloud account that owns the destination instance.

## Procedure

1.  Purchase a data synchronization instance by using the Alibaba Cloud account that owns the destination RDS instance. For more information, see [Purchase a data synchronization instance]().

    **Note:** Select **MySQL** for both the source instance and the destination instance. Select **One-Way Synchronization** as the synchronization topology.

2.  Use the Alibaba Cloud account that owns the destination RDS instance to log on to the [DTS console](https://dts-intl.console.aliyun.com/).
3.  In the left-side navigation pane, click **Data Synchronization**.
4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column.

    ![Configure a one-way data synchronization task](../images/p46141.png)

6.  Configure the source and destination instances.

    ![Configure the source and destination instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3030359951/p46227.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|Select **RDS Instance**.|
    |Instance Region|The source region that you selected on the buy page. You cannot change the value of this parameter.|
    |Alibaba Cloud Account ID of RDS Instance|Enter the ID of the Alibaba Cloud account that owns the source RDS instance. **Note:** Before you configure this parameter, click RDS Instances of Other Alibaba Cloud Accounts in the **Source Instance Details** section.

![Select an RDS instance that is owned by a different Alibaba Cloud account](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3030359951/p46232.png) |
    |Role Name|Enter the name of the RAM role that you configured earlier in [Before you begin](#section_rdl_qrn_qhb).|
    |RDS Instance ID|Select the ID of the source RDS instance.|
    |Destination Instance Details|Instance Type|Select **RDS Instance**.|
    |Instance Region|The destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |Instance ID|Select the ID of the destination RDS instance.|
    |Database Account|Enter the database account of the destination RDS instance. **Note:** If the database engine of the destination RDS instance is **MySQL 5.5** or **MySQL 5.6**, you do not need to configure the **database account** or **database password**. |
    |Database Password|Enter the password of the destination database account.|
    |Encryption|Select **Non-encrypted** or **SSL-encrypted**. If you want to select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before you configure the data synchronization task. For more information, see [Configure SSL encryption on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96120.htm). **Note:** The **Encryption** parameter is available only for regions in mainland China and the Hong Kong \(China\) region. |

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.
8.  Select the synchronization policy and the objects to be synchronized.

    ![Select objects for a one-way data synchronization task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8030359951/p46145.png)

    |Setting|Description|
    |:------|:----------|
    |Select the processing mode of conflicting tables|    -   **Pre-check and Intercept**: checks whether the destination database contains tables that have the same names as tables in the source database. If the source and destination databases do not contain identical table names, the precheck is passed. Otherwise, an error is returned during precheck and the data synchronization task cannot be started.

**Note:** You can use the object name mapping feature to change the names of the tables that are synchronized to the destination database. You can use this feature if the source and destination databases contain identical table names and the tables in the destination database cannot be deleted or renamed. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md).

    -   **Ignore**: skips the precheck for identical table names in the source and destination databases.

**Warning:** If you select **Ignore**, data consistency is not guaranteed and your business may be exposed to potential risks.

        -   DTS does not synchronize the data records that have the same primary keys as the data records in the destination database during initial full data synchronization. This occurs if the source and destination databases have the same schema. However, DTS synchronizes these data records during incremental data synchronization.
        -   If the source and destination databases have different schemas, initial data synchronization may fail. In this case, only some columns are synchronized or the data synchronization task fails. |
    |Select the objects to be synchronized|Select objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section.

You can select tables or databases as the objects to be synchronized.

**Note:**

    -   If you select a database as the object to be synchronized, all schema changes in the database are synchronized to the destination database.
    -   By default, after an object is synchronized to the destination database, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are synchronized to the destination database. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md). |

9.  In the lower-right corner of the page, click **Next**.
10. Configure initial synchronization.

    ![Advanced settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8030359951/p41055.png)

    -   During an initial synchronization, DTS synchronizes the schemas and data of the required objects from the source instance to the destination instance. The schemas and data are the basis for subsequent incremental synchronization.
    -   Initial synchronization includes initial schema synchronization and initial full data synchronization. In most cases, you need to select both **Initial Schema Synchronization** and **Initial Full Data Synchronization**.
11. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, a precheck is performed. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
12. Close the Precheck dialog box after the following message is displayed: **The precheck is passed.** Then, the data synchronization task starts.
13. Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    You can view the status of the data synchronization task on the Synchronization Tasks page.

    ![Status of the data synchronization task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5130359951/p41059.png)


