# Migrate data from an ApsaraDB RDS for MySQL instance to a PolarDB for MySQL cluster

PolarDB is a next-generation relational database service that is developed by Alibaba Cloud. PolarDB is compatible with the MySQL database engine and features high availability, ease of use, and reliability. This topic describes how to migrate data from an ApsaraDB RDS for MySQL instance to a PolarDB for MySQL cluster by using Data Transmission Service \(DTS\).

A PolarDB for MySQL cluster is created. For more information, see [Create a PolarDB for MySQL cluster](https://www.alibabacloud.com/help/zh/doc-detail/58769.htm).

## Precautions

-   DTS uses read and write resources of the source and destination databases during full data migration. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you migrate data, evaluate the impact of data migration on the performance of the source and destination databases. We recommend that you migrate data during off-peak hours. For example, you can migrate data when the CPU utilization of the source and destination databases is less than 30%.
-   The source database must have PRIMARY KEY or UNIQUE constraints and all fields must be unique. Otherwise, the destination database may contain duplicate data records.
-   During full data migration, concurrent INSERT operations cause fragmentation in the tables of the destination database. After full data migration is complete, the tablespace of the destination database is larger than that of the source database.
-   If a data migration task fails, DTS automatically resumes the task. Before you switch your workloads to the destination database, stop or release the data migration task. Otherwise, the data in the source database will overwrite the data in the destination database after the task is resumed.

## Limits

-   DTS supports schema migration for the following types of objects: table, view, trigger, stored procedure, and function.

    **Note:** During schema migration, DTS changes the value of the SECURITY attribute from `DEFINER` to `INVOKER` for views, stored procedures, and functions.

-   DTS does not migrate user information from the source database. After the migration is complete, to call a view, stored procedure, or function of the destination database, you must grant the read and write permissions to INVOKER.

## Migration types

DTS supports schema migration, full data migration, and incremental data migration. For more information, see [Terms]().

**Note:** When you migrate data between Apsara PolarDB for MySQL clusters, you can select all of the supported migration types to ensure service continuity.

## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|--------------|--------------------------|--------------------|
|Schema migration and full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|
|Incremental data migration|Charged. For more information, see [Pricing]().|

## SQL operations that can be synchronized during incremental data migration

|Operation type|SQL statement|
|--------------|-------------|
|DML|INSERT, UPDATE, DELETE, and REPLACE|
|DDL|-   ALTER TABLE and ALTER VIEW
-   CREATE FUNCTION, CREATE INDEX, CREATE PROCEDURE, CREATE TABLE, and CREATE VIEW
-   DROP INDEX and DROP TABLE
-   RENAME TABLE
-   TRUNCATE TABLE |

## Permissions required for database accounts

|Database|Required permissions|
|:-------|:-------------------|
|ApsaraDB RDS for MySQL|The read permission on the objects to be migrated|
|PolarDB for MySQL|The read and write permissions on the objects to be migrated|

**Note:** For more information about how to create and authorize a database account, see [Create an account on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96089.htm) and [Create a database account](https://www.alibabacloud.com/help/zh/doc-detail/68508.htm).

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Data Migration**.

3.  At the top of the Migration Tasks page, select the region where the destination cluster resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.

5.  Configure the source and destination databases.

    ![Configure the source and destination databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3403019951/p57983.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|Instance Type|Select **RDS Instance**.|
    |Instance Region|Select the region where the source RDS instance resides.|
    |Database Account|Enter the database account of the source RDS instance. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_729_3zj_iiv).|
    |Database Password|Enter the password of the source database account. **Note:** After you specify the source database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
    |Encryption|Select **Non-encrypted** or **SSL-encrypted**. If you want to select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before you configure the data migration task. For more information, see [Configure SSL encryption on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96120.htm). **Note:** The **Encryption** parameter is available only for regions in mainland China and the China \(Hong Kong\) region. |
    |Destination Database|Instance Type|Select **PolarDB**.|
    |Instance Region|Select the region where the destination PolarDB cluster resides.|
    |PolarDB Instance ID|Select the ID of the destination PolarDB cluster.|
    |Database Account|Enter the database account of the destination PolarDB cluster. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_729_3zj_iiv).|
    |Database Password|Enter the password of the destination database account. **Note:** After you specify the destination database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the destination database parameters based on the check results. |

6.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelists of the source ApsaraDB RDS for MySQL instance and the destination PolarDB for MySQL cluster. This ensures that DTS servers can connect to the source instance and the destination cluster.

7.  Select the migration types and objects to be migrated.

    ![Select the migration types and objects to be migrated](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2154204061/p56880.png)

    |Parameter|Description|
    |:--------|:----------|
    |Migration Types|    -   To perform only full data migration, select **Schema Migration** and **Full Data Migration**.
    -   To ensure service continuity during data migration, select **Schema Migration**, **Full Data Migration**, and **Incremental Data Migration**.
**Note:** If **Incremental Data Migration** is not selected, do not write data to the source database during full data migration. This ensures data consistency between the source and destination databases. |
    |Objects|Select objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section.

**Note:**

    -   You can select columns, tables, or databases as the objects to be migrated.
    -   After an object is migrated to the destination database, the name of the object remains unchanged. You can change the names of the objects that are migrated to the destination database by using the object name mapping feature. For more information about how to use this feature, see [Object name mapping](/intl.en-US/Data Migration/Migration task management/Object name mapping.md).
    -   If you use the object name mapping feature on an object, other objects that are dependent on the object may fail to be migrated. |

8.  In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data migration task, a precheck is performed. A data migration task can be started only if it passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and perform a precheck again.
9.  After the task passes the precheck, click **Next**.

10. In the Confirm Settings dialog box, specify the **Channel Specification** parameter and select **Data Transmission Service \(Pay-As-You-Go\) Service Terms**.

11. Click **Buy and Start** to start the data migration task.

    -   Schema migration and full data migration

        We recommend that you do not manually stop a data migration task. Otherwise, data migrated to the destination database will be incomplete. Wait until the data migration task automatically stops.

    -   Schema migration, full data migration, and incremental data migration

        An incremental data migration task does not automatically stop. You must manually stop the migration task.

        **Note:** Select an appropriate time to manually stop the migration task. For example, you can stop the migration task during off-peak hours or before you switch your workloads to the destination instance.

        1.  Wait until **Incremental Data Migration** and **The migration task is not delayed** appear in the progress bar of the migration task. Then, stop writing data to the source database for a few minutes. The delay time of **incremental data migration** may be displayed in the progress bar.
        2.  After the status of **incremental data migration** changes to **The migration task is not delayed**, manually stop the migration task.

            ![Stop an incremental data migration task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2940359951/p47604.png)

12. Switch your workloads to the destination PolarDB cluster.


