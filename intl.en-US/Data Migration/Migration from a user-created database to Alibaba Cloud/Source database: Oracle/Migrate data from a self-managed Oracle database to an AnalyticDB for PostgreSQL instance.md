# Migrate data from a self-managed Oracle database to an AnalyticDB for PostgreSQL instance

This topic describes how to migrate data from a self-managed Oracle database to an AnalyticDB for PostgreSQL instance by using Data Transmission Service \(DTS\).

-   The version of the self-managed Oracle database is 9i, 10g, 11g, 12c, 18c, or 19c.
-   The self-managed Oracle database is running in ARCHIVELOG mode. Archived log files are accessible and a suitable retention period is set for archived log files. For more information, see [Managing Archived Redo Log Files](https://docs.oracle.com/database/121/ADMIN/archredo.htm#ADMIN008).
-   Supplemental logging, including SUPPLEMENTAL\_LOG\_DATA\_PK and SUPPLEMENTAL\_LOG\_DATA\_UI, is enabled for the self-managed Oracle database. For more information, see [Supplemental Logging](https://docs.oracle.com/database/121/SUTIL/GUID-D857AF96-AC24-4CA1-B620-8EA3DF30D72E.htm#SUTIL1582).
-   An AnalyticDB for PostgreSQL instance is created in the china \(Shanghai\), China \(Beijing\), or China \(Shenzhen\) region. For more information, see [创建云原生数据仓库AnalyticDB PostgreSQL实例](https://help.aliyun.com/document_detail/50200.html)[Create an AnalyticDB for PostgreSQL instance](https://www.alibabacloud.com/help/zh/doc-detail/50200.htm).

## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|--------------|--------------------------|--------------------|
|Schema migration and full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|
|Incremental data migration|Charged. For more information, see [Pricing]().|

## Precautions

-   During full data migration, DTS uses read and write resources of the source and destination databases. This may increase the loads of the database servers. Before you migrate data, evaluate the impact of data migration on the performance of the source and destination databases. We recommend that you migrate data during off-peak hours.
-   If a data migration task fails, DTS automatically resumes the task. Before you switch your workloads to the destination database, stop or release the data migration task. Otherwise, the data from the source database will overwrite the data in the destination database after the task is resumed.

## Migration types

|Migration type|Description|
|--------------|-----------|
|Schema migration|DTS migrates the schemas of the required objects from the source database to the destination database. DTS supports schema migration for the following types of objects: table, index, constraint, function, sequence, and view. **Warning:**

-   Oracle and AnalyticDB for PostgreSQL are heterogeneous databases. DTS does not ensure that the schemas of the source and destination databases are consistent after schema migration. We recommend that you evaluate the impact of data type conversion on your business. For more information, see [Data type mappings between heterogeneous databases](/intl.en-US/Data Migration/Data type mappings between heterogeneous databases.md).
-   For partition tables, DTS discards the partition definitions. You must define partitions in the destination database. |
|Full data migration|DTS migrates historical data of the required objects from the source database to the destination database. **Note:** During schema migration and full data migration, do not perform data definition language \(DDL\) operations on the required objects. Otherwise, the objects may fail to be migrated. |
|Incremental data migration|After full data migration is complete, DTS retrieves redo log files from the source Oracle database. Then, DTS migrates incremental data from the source Oracle database to the destination database in real time. DTS can synchronize the following SQL operations during incremental data migration:

-   Data manipulation language \(DML\) operations: INSERT, UPDATE, and DELETE
-   DDL operation: ADD COLUMN

Incremental data migration allows you to ensure service continuity when you migrate data from an Oracle database to an AnalyticDB for PostgreSQL instance. |

## Permissions required for database accounts

|Database|Schema migration|Full data migration|Incremental data migration|
|:-------|:---------------|:------------------|:-------------------------|
|Self-managed Oracle database|The owner permission on schemas|The owner permission on schemas|DBA|
|AnalyticDB for PostgreSQL|The read and write permissions on the destination database|The read and write permissions on the destination database|The read and write permissions on the destination database|

For more information about how to create and authorize a database account, see the following topics:

-   Self-managed Oracle database: [CREATE USER](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_8003.htm) and [GRANT](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_9013.htm)
-   AnalyticDB for PostgreSQL: [Configure an account](~~50206~~)

**Note:** If you need to migrate incremental data from an Oracle database but the DBA permission cannot be granted to the database account, you can grant fine-grained permissions to the account. The following sample statements show you how to grant specific permissions to an Oracle database account.

## Procedure

1.  Log on to the [数据传输控制台](https://dts.console.aliyun.com/)[DTS console](https://dts-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Data Migration**.

3.  At the top of the **Migration Tasks** page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.

5.  Configure the source and destination databases.

    ![Configure the source and destination databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1689055061/p128448.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|Instance Type|Select an instance type based on the deployment of the source database. In this example, select **User-Created Database in ECS Instance**. **Note:** If you select other instance types, you must prepare the environment that is required for the source database. For more information, see [Preparation overview](). |
    |Instance Region|Select the region of the ECS instance on which the self-managed Oracle database is deployed.|
    |ECS Instance ID|Select the ID of the ECS instance on which the self-managed Oracle database is hosted.|
    |Database Type|Select **Oracle**.|
    |Port Number|Enter the service port number of the self-managed Oracle database. The default port number is **1521**.|
    |Instance Type|    -   **Non-RAC Instance**: If you select this option, you must specify the **SID** parameter.
    -   **RAC Instance**: If you select this option, you must specify the **Service Name**.
 In this example, select **Non-RAC Instance**.|
    |SID|Enter the system ID \(SID\) of the self-managed Oracle database.|
    |Database Account|Enter the account of the self-managed Oracle database. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_ob1_wtk_7lu).|
    |Database Password|Enter the password of the source database account. **Note:** After you specify the source database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
    |Destination Database|Instance Type|Select **AnalyticDB for PostgreSQL**.|
    |Instance Region|Select the region where the destination AnalyticDB for PostgreSQL instance resides.|
    |Instance ID|Select the ID of the destination AnalyticDB for PostgreSQL instance.|
    |Database Name|Enter the name of the destination database.|
    |Database Account|Enter the database account of the destination AnalyticDB for PostgreSQL instance. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_ob1_wtk_7lu).|
    |Database Password|Enter the password of the destination database account.|

6.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    DTS adds the CIDR blocks of DTS servers to the inbound rule of the ECS instance and the whitelist of the AnalyticDB for PostgreSQL instance. This ensures that DTS servers can connect to the source and destination instances.

7.  Select the migration types, the operation types, and the objects to be migrated.

    ![Select the migration types and the objects to be migrated](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7835934061/p128449.png)

    |Setting|Description|
    |:------|:----------|
    |Select the migration types|    -   To perform only full data migration, select **Schema Migration** and **Full Data Migration**.
    -   To ensure service continuity during data migration, select **Schema Migration**, **Full Data Migration**, and **Incremental Data Migration**.
**Note:** If **Incremental Data Migration** is not selected, do not write data to the source database during full data migration. This ensures data consistency between the source and destination databases. |
    |Select the operation types|Select the types of operations that you want to synchronize during incremental data migration. All operation types are selected by default.|
    |Select the objects to be migrated|Select one or more objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section. **Note:**

    -   You can select columns, tables, or schemas as the objects to be migrated.
    -   By default, after an object is migrated to the destination database, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are migrated to the destination database. For more information, see [Object name mapping](/intl.en-US/Data Migration/Migration task management/Object name mapping.md).
    -   If you use the object name mapping feature on an object, other objects that are dependent on the object may fail to be migrated. |
    |Enclose object names in quotation marks|Specify whether you need to enclose object names in quotation marks. If you select **Yes** and the following conditions are met, DTS encloses object names in single or double quotation marks during schema migration and incremental data migration.     -   The business environment of the source database is case-sensitive but the database name contains both uppercase and lowercase letters.
    -   A source table name does not start with a letter and contains characters other than letters, digits, and special characters.

**Note:** A source table name can contain only the following special characters: underscores \(\_\), number signs \(\#\), and dollar signs \($\).

    -   The names of the schemas, tables, or columns that you want to migrate are keywords, reserved keywords, or invalid characters in the destination database. |

8.  Specify the primary key column and distribution key of the table that you want to migrate to the AnalyticDB for PostgreSQL instance.

    ![Advanced settings: Migrate Oracle to AnalyticDB for PostgreSQL](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7835934061/p128456.png)

    **Note:**

    -   For more information about primary key columns and distribution keys, see [表的约束定义](https://help.aliyun.com/document_detail/118150.html#title-8zk-q8o-q3l)[Define constraints](https://www.alibabacloud.com/help/zh/doc-detail/118150.htm#title-8zk-q8o-q3l) and [表分布键定义](https://help.aliyun.com/document_detail/120143.html)[Define table distribution](https://www.alibabacloud.com/help/zh/doc-detail/120143.htm).
    -   If DTS identifies tables without primary keys, the option **Set Primary Keys and Distribution Keys of All Tables Without Primary Keys to ROWID** is displayed on the preceding page. If you select this option, DTS adds the ROWID field as the primary key and distribution key to the destination table.
9.  In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data migration task, a precheck is performed. DTS can migrate data only if the precheck is past.
    -   If the precheck fails, click the ![Prompt](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Fix the issues as prompted and run the precheck again.
10. After the precheck is past, click **Next**.

11. In the Confirm Settings dialog box, specify the **Channel Specification** parameter and select **Data Transmission Service \(Pay-As-You-Go\) Service Terms**.

12. Click **Buy and Start** to start the data migration task.

    -   Schema migration and full data migration

        Do not manually stop the migration task. Otherwise, the data may be incomplete. You need only to wait until the migration task is complete. The migration task can automatically stop.

    -   Schema migration, full data migration, and incremental data migration

        The migration task cannot automatically stop. You must manually stop the migration task.

        **Note:** Manually stop the migration task at an appropriate time. For example, you can stop the migration task during off-peak hours of your business or before you switch your business to the destination cluster.

        1.  Wait until **Incremental Data Migration** and **The migration task is not delayed** appear in the progress bar of the migration task. Then, stop writing data to the source database for a few minutes. The delay time may appear in the status for **incremental migration**.
        2.  Wait until the status of **Incremental Data Migration** for the migration task changes to **The migration task is not delayed** again. Then, manually stop the migration task.

            ![Stop an incremental migration task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2940359951/p47604.png)


