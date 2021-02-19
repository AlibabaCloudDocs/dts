# Migrate data between RDS instances

This topic describes how to use Data Transmission Service \(DTS\) to migrate data between RDS instances. DTS supports schema migration, full data migration, and incremental data migration. When you configure a data migration task, you can select all of the supported migration types to ensure service continuity.

## Prerequisites

The database types of the RDS instances meet the following requirements.

|Source database|Destination database|
|---------------|--------------------|
|ApsaraDB RDS for MySQL

ApsaraDB RDS for MariaDB TX

|ApsaraDB RDS for MySQL

ApsaraDB RDS for MariaDB TX |
|ApsaraDB RDS for SQL Server|ApsaraDB RDS for SQL Server|
|ApsaraDB RDS for PostgreSQL|ApsaraDB RDS for PostgreSQL|
|ApsaraDB RDS for PPAS|ApsaraDB RDS for PPAS|

## Precautions

-   DTS uses read and write resources of the source and destination databases during full data migration. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you migrate data, evaluate the impact of data migration on the performance of the source and destination databases. We recommend that you migrate data during off-peak hours. For example, you can migrate data when the CPU utilization of the source and destination databases is less than 30%.
-   The tables to be migrated in the source database must have PRIMARY KEY or UNIQUE constraints and all fields must be unique. Otherwise, the destination database may contain duplicate data records.
-   To ensure data consistency, we recommend that you do not write data to the source RDS instance during full data migration.
-   If a data migration task fails, DTS automatically resumes the task. Before you switch your workloads to the destination instance, stop or release the data migration task. Otherwise, the data in the source instance will overwrite the data in the destination instance after the task is resumed.
-   DTS automatically creates a database in the destination RDS instance. However, if the name of the source database is invalid, you must manually create a database in the destination RDS instance before you configure the data migration task.

    **Note:** For more information about how to create a database and the database naming conventions, see [Create accounts and databases for an ApsaraDB RDS for MySQL instance](/intl.en-US/RDS MySQL Database/Quick start/Create accounts and databases for an ApsaraDB RDS for MySQL instance.md).


## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|--------------|--------------------------|--------------------|
|Schema migration and full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|
|Incremental data migration|Charged. For more information, see [Pricing]().|

## Migration types

-   Schema migration

    DTS migrates the schemas of the required objects to the destination instance.

-   Full data migration

    DTS migrates historical data of the required objects from the source RDS instance to the destination RDS instance.

-   Incremental data migration

    After full data migration is complete, DTS synchronizes incremental data from the source RDS instance to the destination RDS instance. Incremental data migration allows you to ensure service continuity when you migrate data between RDS instances.


## SQL operations that can be synchronized during incremental data migration

|Scenario|Operation type|SQL statements|
|--------|--------------|--------------|
|-   Migrate data between ApsaraDB RDS for MySQL instances
-   Migrate data between ApsaraDB RDS for MariaDB TX instances
-   Migrate data from an ApsaraDB RDS for MariaDB TX instance to an ApsaraDB RDS for MySQL instance

|DML|INSERT, UPDATE, DELETE, and REPLACE|
|DDL|-   ALTER TABLE and ALTER VIEW
-   CREATE FUNCTION, CREATE INDEX, CREATE PROCEDURE, CREATE TABLE, and CREATE VIEW
-   DROP INDEX and DROP TABLE
-   RENAME TABLE
-   TRUNCATE TABLE |
|Migrate data between ApsaraDB RDS for SQL Server instances|DML|INSERT, UPDATE, and DELETE**Note:** DTS does not synchronize the UPDATE operations that update only the large fields. |
|DDL|-   ALTER TABLE, including only ADD COLUMN, DROP COLUMN, and RENAME COLUMN
-   CREATE TABLE and CREATE INDEX

**Note:** If a CREATE TABLE operation creates a partition table or a table that contains functions, DTS does not synchronize the operation.

-   DROP TABLE
-   RENAME TABLE
-   TRUNCATE TABLE |
|Migrate data between ApsaraDB RDS for PostgreSQL instances

Migrate data between ApsaraDB RDS for PPAS instances

|DML|INSERT, UPDATE, and DELETE|
|DDL|-   ALTER TABLE and ADD INDEX
-   CREATE TABLE and CREATE INDEX

**Note:** If a CREATE TABLE operation creates a partition table or a table that contains functions, DTS does not synchronize the operation.

-   DROP TABLE
-   RENAME TABLE |

## Permissions required for database accounts

|Scenario|Database|Schema migration|Full data migration|Incremental data migration|
|--------|:-------|:---------------|:------------------|:-------------------------|
|-   Migrate data between ApsaraDB RDS for MySQL instances
-   Migrate data between ApsaraDB RDS for MariaDB TX instances
-   Migrate data from an ApsaraDB RDS for MariaDB TX instance to an ApsaraDB RDS for MySQL instance

|Source instance|The SELECT permission|The SELECT permission|The REPLICATION SLAVE, REPLICATION CLIENT, SHOW VIEW, and SELECT permissions|
|Destination instance|The read and write permissions|The read and write permissions|The read and write permissions|
|Migrate data between ApsaraDB RDS for SQL Server instances|Source instance|The SELECT permission|The SELECT permission|The sysadmin permission|
|Destination instance|The read and write permissions|The read and write permissions|The read and write permissions|
|Migrate data between ApsaraDB RDS for PostgreSQL instances|Source instance|The usage permission on pg\_catalog|The SELECT permission on the objects to be migrated|superuser|
|Destination instance|The create and usage permissions on the objects to be migrated|The permissions of the database owner, including the permissions to perform the INSERT, UPDATE, and DELETE operations**Note:** A standard account of an ApsaraDB RDS for PostgreSQL instance has the required permissions.

|The permissions of the database owner, including the permissions to perform the INSERT, UPDATE, and DELETE operations**Note:** A standard account of an ApsaraDB RDS for PostgreSQL instance has the required permissions. |
|Migrate data between ApsaraDB RDS for PPAS instances|Source instance|The usage permission on pg\_catalog|The SELECT permission on the objects to be migrated|superuser|
|Destination instance|The create and usage permissions on the objects to be migrated|The owner permission on schemas|The owner permission on schemas|

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).
2.  In the left-side navigation pane, click **Data Migration**.
3.  At the top of the Migration Tasks page, select the region where the destination RDS instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.
5.  Configure the source and destination databases.

    ![Migrate data between RDS instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p49618.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|Instance Type|Select **RDS Instance**.|
    |Instance Region|Select the region where the source RDS instance resides.|
    |RDS Instance ID|Select the ID of the source RDS instance. **Note:** The source and destination RDS instances can be the same or different. You can use DTS to migrate data within an RDS instance or between two RDS instances. |
    |Database Name|Enter the name of the source database in the ApsaraDB RDS for PostgreSQL instance. **Note:** This parameter is required only if the database engine of the RDS instance is **PostgreSQL**. |
    |Database Account|Enter the database account of the source RDS instance. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_bjn_5zq_5hb).|
    |Database Password|Enter the password of the source database account. **Note:** After you specify the source database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
    |Encryption|Select **Non-encrypted** or **SSL-encrypted**. If you want to select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before you configure the data migration task. For more information, see [Configure SSL encryption on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96120.htm). **Note:**

This parameter is required only if the database engine of the RDS instance is **MySQL**.

The **Encryption** parameter is available only for regions in mainland China and the China \(Hong Kong\) region. |
    |Destination Database|Instance Type|Select **RDS Instance**.|
    |Instance Region|Select the region where the destination RDS instance resides.|
    |RDS Instance ID|Select the ID of the destination RDS instance. **Note:** The source and destination RDS instances can be the same or different. You can use DTS to migrate data within an RDS instance or between two RDS instances. |
    |Database Name|Enter the name of the destination database in the ApsaraDB RDS for PostgreSQL instance. The name of the destination database can be different from the name of the source database. **Note:** This parameter is required only if the database engine of the RDS instance is **PostgreSQL**. |
    |Database Account|Enter the database account of the destination RDS instance. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_bjn_5zq_5hb).|
    |Database Password|Enter the password of the destination database account. **Note:** After you specify the destination database parameters, click **Test Connectivity** next to **Database Password** to verify whether the parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the destination database parameters based on the check results. |
    |Encryption|Select **Non-encrypted** or **SSL-encrypted**. If you want to select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before you configure the data migration task. For more information, see [Configure SSL encryption on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96120.htm). **Note:** This parameter is required only if the database engine of the RDS instance is **MySQL**.

The **Encryption** parameter is available only for regions in mainland China and the China \(Hong Kong\) region. |

6.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelists of the source and destination RDS instances. This ensures that DTS servers can connect to the source and destination RDS instances.

7.  Select the migration types and the objects to be migrated.

    ![Select the migration types and the objects to be migrated](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8906544061/p47745.png)

    |Setting|Description|
    |:------|:----------|
    |Select the migration types|Select the migration types based on your business requirements. The migration types must be supported by the database engine.

    -   To perform only full data migration, select **Schema Migration** and **Full Data Migration**.
    -   To ensure service continuity during data migration, select **Schema Migration**, **Full Data Migration**, and **Incremental Data Migration**.
**Note:** If **Incremental Data Migration** is not selected, we recommend that you do not write data to the source RDS instance during full data migration. This ensures data consistency between the source and destination instances. |
    |Select the objects to be migrated|Select one or more objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section.

**Note:**

    -   You can select columns, tables, or databases as the objects to be migrated. If you select tables or columns as the objects to be migrated, DTS does not migrate other objects such as views, triggers, and stored procedures to the destination database.
    -   After an object is migrated to the destination database, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are migrated to the destination database. For more information, see [Object name mapping](/intl.en-US/Data Migration/Migration task management/Object name mapping.md).
    -   If you use the object name mapping feature on an object, other objects that are dependent on the object may fail to be migrated. |

8.  In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data migration task, a precheck is performed. You can start the data migration task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
9.  After the task passes the precheck, click **Next**.
10. In the Confirm Settings dialog box, specify the **Channel Specification** and select **Data Transmission Service \(Pay-As-You-Go\) Service Terms**.
11. Click **Buy and Start** to start the migration task.
    -   Full data migration

        Do not manually stop a task during full data migration. Otherwise, data migrated to the destination database will be incomplete. Wait until the migration task automatically stops.

    -   Incremental data migration

        An incremental data migration task does not automatically stop. You must manually stop the migration task.

        **Note:** Select an appropriate time to manually stop the migration task. For example, you can stop the migration task during off-peak hours or before you switch your workloads to the destination instance.

        1.  Wait until **Incremental Data Migration** and **The migration task is not delayed** appear in the progress bar of the migration task. Then, stop writing data to the source database for a few minutes. The delay time of **incremental data migration** may be displayed in the progress bar.
        2.  After the status of **incremental data migration** changes to **The migration task is not delayed**, manually stop the migration task.

            ![The migration task is not delayed](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2940359951/p47604.png)


