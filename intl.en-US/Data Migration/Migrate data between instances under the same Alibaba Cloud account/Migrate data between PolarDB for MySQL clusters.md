# Migrate data between PolarDB for MySQL clusters

PolarDB is a next-generation relational distributed cloud native database service that is developed by Alibaba Cloud. PolarDB is fully compatible with MySQL and features benefits such as ease of use, high performance, high availability, and high reliability. You can use Data Transmission Service \(DTS\) to migrate data between PolarDB for MySQL clusters.

**Note:** PolarDB does not support cross-version upgrade. For example, you cannot directly upgrade PolarDB for MySQL 5.6 to PolarDB for MySQL 8.0. However, you can create a PolarDB for MySQL cluster of a new version, such as 8.0, and use this method to migrate data from the source cluster to the new cluster. Before you migrate data across versions of PolarDB for MySQL clusters, we recommend that you create a pay-as-you-go PolarDB cluster to test the compatibility. After the test is complete, you can release the cluster.

## Prerequisites

-   The source and destination PolarDB for MySQL clusters are purchased. For more information, see [Create a PolarDB for MySQL cluster](https://www.alibabacloud.com/help/zh/doc-detail/58769.htm).
-   Binary logging is enabled for the source PolarDB for MySQL cluster to meet the requirement of incremental data migration. For more information, see [Enable binary logging](https://www.alibabacloud.com/help/zh/doc-detail/113546.htm).

## Notes

-   DTS uses read and write resources of the source and destination databases during full data migration. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you migrate data, evaluate the impact of data migration on the performance of the source and destination databases. We recommend that you migrate data during off-peak hours. For example, you can migrate data when the CPU utilization of the source and destination databases is less than 30%.
-   If the source database does not have a PRIMARY KEY or UNIQUE constraint and all the fields are not unique, the destination database may contain duplicate data.
-   If a data migration task fails, DTS automatically restores the task. Before you switch your business to the destination database, stop or release the data migration task. Otherwise, the data from the source database overwrites the data in the destination database after the task is restored.

## Introduction to migration types

DTS supports schema migration, full data migration, and incremental data migration. For more information, see [Terms]().

**Note:** When you select all of the three migration types, data between databases can be smoothly migrated.

## Billing description

|Migration type|Link configuration fee|Data transfer cost|
|--------------|----------------------|------------------|
|Schema migration and full data migration|Free of charge.|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|
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

|Database|Required permission|
|:-------|:------------------|
|Source PolarDB for MySQL|The read permission on the objects to be migrated|
|Destination PolarDB for MySQL|The read and write permissions on the objects to be migrated|

**Note:** For more information about how to create and authorize a database account, see [Create a PolarDB database account](https://www.alibabacloud.com/help/zh/doc-detail/68508.htm).

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Data Migration**.

3.  At the top of the Migration Tasks page, select the region where the destination cluster resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.

5.  Configure the connection information about the source and destination databases for the data migration task.

    ![Configure the connection information about the source and destination databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5403019951/p56870.png)

    |Category|Configuration|Description|
    |:-------|:------------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You are not required to use a unique task name.|
    |Source Database|Instance Type|Select **PolarDB**.|
    |Instance Region|Select the region where the source PolarDB cluster resides.|
    |PolarDB Instance ID|Select the ID of the source PolarDB cluster.|
    |Database Account|Enter the database account of the connected source PolarDB cluster.|
    |Database Password|Enter the password of the database account. **Note:** After you enter the source database information, you can click **Test Connectivity** next to **Database Password** to verify whether the entered information valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
    |Destination Database|Instance Type|Select **PolarDB**.|
    |Instance Region|Select the region where the destination PolarDB cluster resides.|
    |PolarDB Instance ID|Select the ID of the destination PolarDB cluster.|
    |Database Account|Enter the database account of the destination PolarDB cluster.|
    |Database Password|Enter the password of the database account. **Note:** After you enter the destination database information, you can click **Test Connectivity** next to **Database Password** to verify whether the entered information is valid. If the entered information is valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Adjust the destination database information based on the prompt. |

6.  After you finish the preceding configuration, click **Set Whitelist and Next** in the lower-right corner of the page.

    **Note:** In this step, DTS automatically adds the IP address of the DTS server to the whitelists of the source and destination PolarDB clusters. This ensures that the DTS server can connect to the source and destination clusters as expected.

7.  Select the migration types and the objects to be migrated.

    ![Select the objects to be migrated and the migration types](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2154204061/p56880.png)

    |Configuration|Description|
    |:------------|:----------|
    |Migration Types|    -   To perform only full migration, select both **Schema Migration** and **Full Data Migration**.
    -   To ensure service continuity during data migration, select **Schema Migration**, **Full Data Migration**, and **Incremental Data Migration**.
**Note:** If **Incremental Data Migration** is not selected, do not write new data to the source database during full data migration. This ensures that the data between the source and destination databases is consistent. |
    |Objects|In the Available section, select the objects to be migrated. Then, click the ![Rightwards arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to add them to the Selected section.

**Note:**

    -   You can select databases, tables, and columns as the objects to be migrated.
    -   By default, the name of the migrated object in the destination database remains consistent with the name of the object in the source database. You can change the names of the objects that are migrated to the destination database by using the object name mapping feature. For more information, see [Object name mapping](/intl.en-US/Data Migration/Migration task management/Object name mapping.md).
    -   If you use the object name mapping feature on an object, other objects that are dependent on the object may fail to be migrated. |

8.  In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data migration task, a precheck is performed. DTS can migrate data only if the precheck is past.
    -   If the precheck fails, click the ![Prompt](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Fix the issues as prompted and run the precheck again.
9.  After the precheck is past, click **Next**.

10. In the Confirm Settings dialog box, specify the **Channel Specification** parameter and select **Data Transmission Service \(Pay-As-You-Go\) Service Terms**.

11. Click **Buy and Start** to start the data migration task.

    -   Schema migration and full data migration

        Do not manually stop the migration task. Otherwise, the data may be incomplete. You need only to wait until the migration task is complete. The migration task can automatically stop.

    -   Schema migration, full data migration, and incremental data migration

        The migration task cannot automatically stop. You must manually stop the migration task.

        **Note:** Manually stop the migration task at an appropriate time. For example, you can stop the migration task during off-peak hours of your business or before you switch your business to the destination cluster.

        1.  Wait until **Incremental Data Migration** and **The migration task is not delayed** appear in the progress bar of the migration task. Then, stop writing data to the source database for a few minutes. The delay time may appear in the status for **incremental migration**.
        2.  Wait until the status of **Incremental Data Migration** for the migration task changes to **The migration task is not delayed** again. Then, manually stop the migration task.

            ![Stop an incremental migration task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2940359951/p47604.png)

12. Switch your business to the PolarDB cluster.


The database accounts that are used for data migration have the read and write permissions. To ensure database security, delete the relevant database accounts after data migration is complete.

