# Migrate data from a user-created Oracle database to a PolarDB cluster compatible with Oracle

Apsara PolarDB is a next-generation relational database service developed by Alibaba Cloud. It is compatible with MySQL, PostgreSQL, and Oracle database engines. The superior performance of Apsara PolarDB in storage and computing meets diverse requirements of enterprises. This topic describes how to migrate data from a user-created Oracle database to a PolarDB cluster compatible with Oracle by using Data Transmission Service \(DTS\).

-   The version of the user-created Oracle database is 9i, 10g, 11g, 12c, 18c, or 19c.
-   The ARCHIVELOG mode is enabled for the user-created Oracle database. Archived log files are accessible and a suitable retention period is set for archived log files. For more information, see [Managing Archived Redo Log Files](https://docs.oracle.com/database/121/ADMIN/archredo.htm#ADMIN008).
-   Supplemental logging, including SUPPLEMENTAL\_LOG\_DATA\_PK and SUPPLEMENTAL\_LOG\_DATA\_UI, is enabled for the user-created Oracle database. For more information, see [Supplemental Logging](https://docs.oracle.com/database/121/SUTIL/GUID-D857AF96-AC24-4CA1-B620-8EA3DF30D72E.htm#SUTIL1582).
-   The tables to be migrated from the user-created Oracle database contain primary keys or UNIQUE NOT NULL indexes.

## Precautions

-   DTS uses read and write resources of the source and destination databases during full data migration. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you migrate data, evaluate the impact of data migration on the performance of the source and destination databases. We recommend that you migrate data during off-peak hours. For example, you can migrate data when the CPU utilization of the source and destination databases is less than 30%.
-   DTS automatically resumes a failed data migration task. Before you switch your workloads to the destination database, stop or release the data migration task. Otherwise, the data in the source database will overwrite the data in the destination database after the task is resumed.

## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|--------------|--------------------------|--------------------|
|Schema migration and full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|
|Incremental data migration|Charged. For more information, see [Pricing]().|

## Migration types

|Migration type|Description|
|--------------|-----------|
|Schema migration|DTS migrates the schemas of the required objects to the destination database. DTS supports schema migration for the following types of objects: table, view, synonym, trigger, stored procedure, function, package, and user-defined type. **Note:** However, if an object contains triggers, the data between the source and destination databases will become inconsistent. |
|Full data migration|DTS migrates historical data of the required objects from the user-created Oracle database to the destination PolarDB cluster. **Note:** During schema migration and full data migration, do not perform DDL operations on the objects to be migrated. Otherwise, the objects may fail to be migrated. |
|Incremental data migration|DTS retrieves redo log files from the user-created Oracle database. Then, DTS synchronizes incremental data from the user-created Oracle database to the destination PolarDB cluster. Incremental data migration allows you to ensure service continuity when you migrate data from the user-created Oracle database to the destination PolarDB cluster.|

## SQL operations that can be synchronized during incremental data migration

-   INSERT, UPDATE, and DELETE operations
-   CREATE TABLE operations

    **Note:** The CREATE TABLE operations to create partition tables or tables that contain functions cannot be synchronized.

-   ALTER TABLE, DROP TABLE, RENAME TABLE, CREATE INDEX, and ADD INDEX operations

## Preparations

Log on to the source Oracle database, create an account for data collection, and grant permissions to the account.

**Note:** If you have created a database account and the account has the permissions that are listed in the following table, skip this step.

|Database|Schema migration|Full data migration|Incremental data migration|
|:-------|:---------------|:------------------|:-------------------------|
|User-created Oracle database|The owner permission on schemas|The owner permission on schemas|The database administrator \(DBA\) permission|
|PolarDB cluster|The owner permission on schemas|The owner permission on schemas|The owner permission on schemas|

For more information about how to create and authorize a database account, see the following topics:

-   [CREATE USER](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_8003.htm) and [GRANT](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_9013.htm) for a user-created Oracle database
-   [Create an account](~~118194~~) for a PolarDB cluster

**Note:** If you want to migrate incremental data from an Oracle database but the DBA permission cannot be granted to the database account, you can grant fine-grained permissions to the account. The following sample statements show you how to grant specific permissions to an Oracle database account.



## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Data Migration**.

3.  At the top of Migration Tasks the page, select the region where the destination cluster resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.

5.  Configure the source and destination databases for the data migration task.

    ![Configure the source and destination databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1863158951/p68785.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|Instance Type|Select an instance type based on where the source database is deployed. The procedure in this topic uses **User-Created Database with Public IP Address** as an example. **Note:** If you select other instance types, you must prepare the environments that are required for the source database. For more information, see [Preparation overview](). |
    |Instance Region|If the instance type is set to **User-Created Database with Public IP Address**, you do not need to specify the **instance region**. **Note:** If a whitelist is configured for the user-created Oracle database, you must manually add the CIDR blocks of DTS servers to the whitelist of the user-created Oracle database. You can click **Get IP Address Segment of DTS** next to **Instance Region** to obtain the CIDR blocks of DTS servers. |
    |Database Type|Select **Oracle**.|
    |Hostname or IP Address|Enter the endpoint that is used to connect to the user-created Oracle database.|
    |Port Number|Enter the service port number of the user-created Oracle database. The default port number is **1521**. **Note:** In this example, The service port of the user-created Oracle database is accessible over the Internet. |
    |Instance Type|    -   **Non-RAC Instance**: If you select this option, you must specify the **SID**.
    -   **RAC Instance**: If you select this option, you must specify the **Service Name**. |
    |Database Account|Enter the account of the user-created Oracle database. For more information about the permissions that are required for the account, see [Preparations](#section_m4e_lp6_p1o).|
    |Database Password|Enter the password for the source database account. **Note:** After you specify the source database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
    |Destination Database|Instance Type|Select **PolarDB**.|
    |Instance Region|Select the region where the destination PolarDB cluster resides.|
    |PolarDB Instance ID|Select the ID of the destination PolarDB cluster.|
    |Database Name|Enter the name of the destination database.|
    |Database Account|Enter the database account of the destination PolarDB cluster. For more information about the permissions that are required for the account, see [Preparations](#section_m4e_lp6_p1o).|
    |Database Password|Enter the password for the destination database account. **Note:** After you specify the destination database parameters, click **Test Connectivity** next to **Database Password** to verify whether the parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the destination database parameters based on the check results. |

6.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** The CIDR blocks of DTS servers are automatically added to the whitelist of the destination PolarDB cluster. This ensures that DTS servers can connect to the destination PolarDB cluster.

7.  Select the migration types and objects to be migrated.

    ![Select the migration types and objects to be migrated](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1863158951/p68786.png)

    |Parameter|Description|
    |:--------|:----------|
    |Migration Types|    -   To perform only full data migration, select **Schema Migration** and **Full Data Migration**.
    -   To migrate data with minimal downtime, select **Schema Migration**, **Full Data Migration**, and **Incremental Data Migration**.
 **Note:** If **Incremental Data Migration** is not selected, do not write data into the source database during full data migration. This ensures data consistency between the source and destination databases. |
    |Objects|Select objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section.

 **Note:**

    -   You can select columns, tables, or databases as the objects to be migrated.
    -   After an object is migrated to the destination database, the name of the object remains the same as that in the source database. You can change the names of the objects that are migrated to the destination database by using the object name mapping feature. For more information about how to use this feature, see [Object name mapping](/intl.en-US/Data Migration/Migration task management/Object name mapping.md).
    -   If you use the object name mapping feature on an object, other objects that are dependent on the object may fail to be migrated. |

8.  Click **Precheck** on the lower right of the page.

    **Note:**

    -   A precheck is performed for a data migration task. A data migration task can be started only if it passes the precheck.
    -   If the precheck fails, click ![Note](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon corresponding to each failed item to view the details. Fix the problems as instructed and run the precheck again.
9.  After the precheck is passed, click **Next**.

10. On the Confirm Settings dialog box that appears, specify **Channel Specification** and select the **Data Transmission Service \(Pay-As-You-Go\) Service Terms**.

11. Click **Buy and Start** to start the data migration task.

    -   Schema migration and full data migration

        Do not manually stop a migration task. Otherwise, data migrated to the destination database will be incomplete. Wait until the data migration task stops when it is complete.

    -   Schema migration, full data migration, and incremental data migration

        An incremental data migration task does not automatically end. You must manually end the migration task.

        **Note:** Select an appropriate time point to manually end the migration task. For example, you can end the migration task during off-peak hours or before you switch your workloads to the destination cluster.

        1.  When the task progress bar switches to **Incremental Data Migration** and the message **The migration task is not delayed** appears, stop writing new data to the source database for a few minutes. Then, the progress bar will show the latency of the **incremental data migration**.
        2.  When the status of **incremental data migration** changes to **The migration task is not delayed**, manually stop the migration task.

            ![Stop the incremental migration task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2940359951/p47604.png)

12. Switch your workloads to the destination cluster.


