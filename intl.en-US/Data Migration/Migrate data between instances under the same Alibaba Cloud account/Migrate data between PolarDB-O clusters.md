# Migrate data between PolarDB-O clusters

PolarDB is a next-generation relational database service that is developed by Alibaba Cloud. It is compatible with MySQL, PostgreSQL, and Oracle database engines. PolarDB provides superior performance in storage and computing to meet diverse requirements of enterprises. This topic describes how to migrate data between PolarDB cluster compatible with Oracle clusters by using Data Transmission Service \(DTS\).

-   The source and destination PolarDB-O clusters reside in the China \(Shanghai\) region. This is because data migration between PolarDB-O clusters is available only in the China \(Shanghai\) region.
-   The tables to be migrated from the source PolarDB-O cluster contain primary keys or UNIQUE NOT NULL indexes.
-   The value of the wal\_level parameter is set to logical for the source PolarDB-O cluster. This setting ensures that logical decoding is supported in write-ahead logging \(WAL\). For more information, see [Change the values of cluster parameters]().

## Precautions

-   DTS uses read and write resources of the source and destination databases during full data migration. This may increase the load of the database server. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you migrate data, evaluate the impact of data migration on the performance of the source and destination databases. We recommend that you migrate data during off-peak hours. For example, you can migrate data when the CPU utilization of the source and destination databases is less than 30%.
-   A single data migration task can migrate data from only one database. To migrate data from multiple databases, you must create a data migration task for each database.
-   If you select a schema as the object to be migrated and create a table in the schema or run the RENAME command to rename the table, you must run the `ALTER TABLE schema.table REPLICA IDENTITY FULL;` command before you write data to the table.

    **Note:** Replace the `schema` and `table` in the preceding sample command with the actual schema name and table name.

-   To ensure that the delay time of incremental data migration is accurate, DTS adds a heartbeat table named `dts_postgres_heartbeat` to the source database. The following figure shows the schema of the heartbeat table.

    ![Schema of a heartbeat table](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2310398951/p94992.png)

-   If a data migration task fails, DTS automatically resumes the task. Before you switch your workloads to the destination database, stop or release the data migration task. Otherwise, the data from the source database will overwrite the data in the destination database after the task is resumed.

## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|--------------|--------------------------|--------------------|
|Schema migration and full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|
|Incremental data migration|Charged. For more information, see [Pricing]().|

## Migration types

|Migration type|Description|
|--------------|-----------|
|Schema migration|DTS migrates the schemas of the required objects to the destination PolarDB-O cluster. DTS supports schema migration for the following types of objects: table, view, synonym, trigger, stored procedure, function, package, and user-defined type. **Note:** However, if an object contains triggers, data will become inconsistent between the source and destination databases. |
|Full data migration|DTS migrates historical data of the required objects from the source PolarDB-O cluster to the destination PolarDB-O cluster. **Note:** During schema migration and full data migration, do not perform data definition language \(DDL\) operations on the objects to be migrated. Otherwise, the objects may fail to be migrated. |
|Incremental data migration|DTS retrieves redo log files from the source PolarDB-O cluster. Then, DTS synchronizes incremental data from the source PolarDB-O cluster to the destination PolarDB-O cluster. DTS can synchronize data manipulation language \(DML\) operations, including INSERT, UPDATE, and DELETE operations. DTS cannot synchronize DDL operations.Incremental data migration allows you to ensure service continuity when you migrate data between PolarDB-O clusters. |

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Data Migration**.

3.  At the top of the Migration Tasks page, select the region where the destination cluster resides.

    ![Select a region](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.

5.  Configure the source and destination databases.

    ![Migrate data between PolarDB-O clusters](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4534948951/p94892.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|Instance Type|Select **PolarDB**.|
    |Instance Region|Select the region where the source PolarDB-O cluster resides.|
    |PolarDB Instance ID|Select the ID of the source PolarDB-O cluster.|
    |Database Name|Enter the name of the source database.|
    |Database Account|Enter the privileged account of the source PolarDB-O cluster. For more information, see [Create database accounts]().|
    |Database Password|Enter the password of the source database account. **Note:** After you specify the source database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
    |Destination Database|Instance Type|Select **PolarDB**.|
    |Instance Region|Select the region where the destination PolarDB-O cluster resides.|
    |PolarDB Instance ID|Select the ID of the destination PolarDB-O cluster.|
    |Database Name|Enter the name of the destination database.|
    |Database Account|Enter the database account of the destination PolarDB-O cluster. The account must have the **owner permission on the database**. **Note:** You can specify the **database owner** when you create a database. |
    |Database Password|Enter the password of the destination database account. **Note:** After you specify the destination database parameters, click **Test Connectivity** next to **Database Password** to verify whether the parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the destination database parameters based on the check results. |

6.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    DTS adds the CIDR blocks of DTS servers to the whitelists of the source and destination PolarDB-O clusters. This ensures that DTS servers can connect to the source and destination PolarDB-O clusters.

7.  Select the migration types and the objects to be migrated.

    ![Select the objects to be migrated](../images/p101902.png)

    |Parameter|Description|
    |:--------|:----------|
    |Migration Types|    -   To perform only full data migration, select **Schema Migration** and **Full Data Migration**.
    -   To ensure service continuity during data migration, select **Schema Migration**, **Full Data Migration**, and **Incremental Data Migration**.
**Note:**

    -   If **Incremental Data Migration** is not selected, do not write data to the source database during full data migration. This ensures data consistency between the source and destination databases.
    -   During schema migration and full data migration, do not perform DDL operations on the objects to be migrated. Otherwise, the objects may fail to be migrated. |
    |Objects|Select objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section. You can select columns, tables, or schemas as the objects to be migrated. **Note:**

    -   After an object is migrated to the destination database, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are migrated to the destination PolarDB-O cluster. For more information, see [Object name mapping](/intl.en-US/Data Migration/Migration task management/Object name mapping.md).
    -   If you use the object name mapping feature on an object, other objects that are dependent on the object may fail to be migrated. |

8.  In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data migration task, a precheck is performed. A data migration task can be started only if it passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and perform a precheck again.
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

            ![Stop an incremental data migration task](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2940359951/p47604.png)


## References

[Switch workloads to the destination database](/intl.en-US/Data Migration/Migration task management/Switch workloads to the destination database.md)

