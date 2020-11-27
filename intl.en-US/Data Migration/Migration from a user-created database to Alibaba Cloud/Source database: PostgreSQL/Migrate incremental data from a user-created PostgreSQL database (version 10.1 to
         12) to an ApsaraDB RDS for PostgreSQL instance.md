# Migrate incremental data from a user-created PostgreSQL database \(version 10.1 to 12\) to an ApsaraDB RDS for PostgreSQL instance

This topic describes how to migrate incremental data from a user-created PostgreSQL database to an ApsaraDB RDS for PostgreSQL instance by using Data Transmission Service \(DTS\). DTS supports schema migration, full data migration, and incremental data migration. When you migrate data from a user-created PostgreSQL database to Alibaba Cloud, you can select all of the supported migration types to ensure service continuity.

-   A PostgreSQL database of version 10.1 to 12 is created.
-   An ApsaraDB RDS for PostgreSQL instance is created. For more information, see [Create an ApsaraDB RDS for PostgreSQL instance](~~53730~~).

    **Note:** Make sure that the database version of the ApsaraDB RDS for PostgreSQL instance is the same as the version of the user-created PostgreSQL database.

-   The available storage space of the ApsaraDB RDS for PostgreSQL instance is larger than the total size of the data in the user-created PostgreSQL database.

## Precautions

-   DTS uses read and write resources of the source and destination databases during full data migration. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you migrate data, evaluate the impact of data migration on the performance of the source and destination databases. We recommend that you migrate data during off-peak hours. For example, you can migrate data when the CPU utilization of the source and destination databases is less than 30%.
-   The tables to be migrated in the source database must have PRIMARY KEY or UNIQUE constraints and all fields must be unique. Otherwise, the destination database may contain duplicate data records.
-   If you select a schema as the object to be migrated and create a table in the schema or run the RENAME command to rename the table, you must run the `ALTER TABLE schema.table REPLICA IDENTITY FULL;` command before you write data to the table.

    **Note:** Replace the `schema` and `table` in the preceding sample command with the actual schema name and table name.

-   To ensure that the delay time of incremental data migration is accurate, DTS adds a heartbeat table named dts\_postgres\_heartbeat to the source database.
-   During incremental data migration, DTS creates a replication slot for the source database. The replication slot is prefixed with `dts_sync_`. DTS automatically clears historical replication slots every 90 minutes to reduce storage usage.

    **Note:** If a migration task is released or fails, DTS automatically clears the replication slot. After a switchover between primary and secondary ApsaraDB RDS for PostgreSQL databases, you must log on to the secondary database to manually clear the replication slot.

    ![Replication slot information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5997539951/p66313.png)

-   If a data migration task fails, DTS automatically resumes the task. Before you switch your workloads to the destination instance, stop or release the data migration task. Otherwise, the data in the source database will overwrite the data in the destination instance after the task is resumed.

## Limits

-   A single data migration task can migrate data from only one database. To migrate data from multiple databases, you must create a data migration task for each database.
-   The name of the source database cannot contain hyphens \(-\), for example, dts-testdata.
-   Incremental migration does not support resumable transmission after a switchover between primary and secondary ApsaraDB RDS for PostgreSQL databases.
-   Data may be inconsistent between the primary and secondary nodes of the source database due to synchronization delay. Therefore, you must use the primary node as the data source when you migrate data.

    **Note:** We recommend that you migrate data during off-peak hours. You can modify the transfer rate of full data migration based on the read/write performance of the source database. For more information, see [Modify the transfer rate of full data migration](/intl.en-US/Data Migration/Migration task management/Modify the transfer rate of full data migration.md).

-   During incremental data migration, DTS migrates only data manipulation language \(DML\) operations. DML operations include INSERT, DELETE, and UPDATE.

    **Note:** Only data migration tasks that are created after October 1, 2020 can migrate data definition language \(DDL\) operations. To do this, you must create a trigger and function in the source database to obtain the DDL information before you configure the task. For more information, see [Use triggers and functions to implement incremental DDL migration for PostgreSQL databases](/intl.en-US/Best Practices/Use triggers and functions to implement incremental DDL migration for PostgreSQL databases.md).

-   After your workloads are switched to the destination database, newly written sequences do not increment from the maximum value of the sequences in the source database. Therefore, you must query the maximum value of the sequences in the source database before you switch your workloads to the destination database. Then, you must specify the queried maximum value as the starting value of the sequences in the destination database.
-   DTS does not check the validity of metadata such as sequences. You must manually check the validity of metadata.

## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|--------------|--------------------------|--------------------|
|Schema migration and full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|
|Incremental data migration|Charged. For more information, see [Pricing]().|

## Permissions required for database accounts

|Database|Schema migration|Full data migration|Incremental data migration|
|:-------|:---------------|:------------------|:-------------------------|
|User-created PostgreSQL database|The usage permission on pg\_catalog|The SELECT permission on the objects to be migrated|superuser|
|ApsaraDB RDS for PostgreSQL instance|The create and usage permissions on the objects to be migrated|The owner permission on schemas|The owner permission on schemas|

For more information about how to create and authorize a database account, see the following topics:

-   User-created PostgreSQL database: [CREATE USER](https://www.postgresql.org/docs/10/sql-createuser.html) and [GRANT](https://www.postgresql.org/docs/10/sql-grant.html)
-   ApsaraDB RDS for PostgreSQL instance: [Create an account for an ApsaraDB RDS for PostgreSQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96753.htm)

## Data migration process

To avoid data migration failures caused by dependencies between objects, DTS migrates the schemas and data of the source PostgreSQL database in the following order:

**Note:** For more information about schema migration, full data migration, and incremental data migration, see [Terms]().

|Data migration process|Description|
|----------------------|-----------|
|1. Schema migration|DTS migrates the schemas of tables, views, sequences, functions, user-defined types, rules, domains, operations, and aggregates to the destination database. **Note:** DTS does not migrate plug-ins. In addition, DTS does not migrate functions that are written in the C programming language. |
|2. Full data migration|DTS migrates historical data of the required objects to the destination database.|
|3. Schema migration|DTS migrates the schemas of triggers and foreign keys to the destination database.|
|4. Incremental data migration|DTS migrates incremental data of the required objects to the destination database. **Note:** Incremental data migration does not support the BIT data type. |

## Before you begin

1.  Log on to the server where the user-created PostgreSQL database resides.

2.  Set the value of the wal\_level parameter in the `postgresql.conf` configuration file to `logical`.

    ![Set the wal_level parameter](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5824948951/p73217.png)

    **Note:** Skip this step if you do not need to perform incremental data migration.

3.  Add the CIDR blocks of DTS servers to the pg\_hba.conf configuration file of the user-created PostgreSQL database. Add only the CIDR blocks of the DTS servers that reside in the same region as the destination database. For more information, see [Add the CIDR blocks of DTS servers to the security settings of on-premises databases]().

    **Note:** For more information, see [The pg\_hba.conf File](https://www.postgresql.org/docs/current/auth-pg-hba-conf.html). Skip this step if you have set the IP address in the pg\_hba.conf file to `0.0.0.0/0`.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5824948951/p73782.png)

4.  Create a trigger and function in the source database to obtain the DDL information. For more information, see [Use triggers and functions to implement incremental DDL migration for PostgreSQL databases](/intl.en-US/Best Practices/Use triggers and functions to implement incremental DDL migration for PostgreSQL databases.md).

    **Note:** Skip this step if you do not need to migrate DDL operations.


## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Data Migration**.

3.  At the top of the Migration Tasks page, select the region where the destination cluster resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.

5.  Configure the source and destination databases.

    ![Configure the source and destination databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1924948951/p47957.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|Instance Type|Select an instance type based on the deployment of the source database. In this example, select **User-Created Database with Public IP Address**. **Note:** If you select other instance types, you must prepare the environment that is required for the source database. For more information, see [Preparation overview](). |
    |Instance Region|If the instance type is set to **User-Created Database with Public IP Address**, you do not need to specify the **instance region**.|
    |Database Type|Select **PostgreSQL**.|
    |Hostname or IP Address|Enter the endpoint that is used to connect to the user-created PostgreSQL database. In this example, enter the public IP address.|
    |Port Number|Enter the service port number of the user-created PostgreSQL database. The port must be accessible over the Internet.|
    |Database Name|Enter the name of the user-created PostgreSQL database.|
    |Database Account|Enter the account that is used to log on to the user-created PostgreSQL database. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_zpw_l1z_ylk).|
    |Database Password|Enter the password of the source database account. **Note:** After you specify the source database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
    |Destination Database|Instance Type|Select **RDS Instance**.|
    |Instance Region|Select the region where the destination RDS instance resides.|
    |RDS Instance ID|Select the ID of the destination RDS instance.|
    |Database Name|Enter the name of the destination database in the RDS instance. The name can be different from the name of the source database. **Note:** Before you configure the data migration task, create a database in the ApsaraDB RDS for PostgreSQL instance. For more information, see [Create a database](~~96758~~). |
    |Database Account|Enter the database account of the destination RDS instance. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_zpw_l1z_ylk).|
    |Database Password|Enter the password of the destination database account. **Note:** After you specify the destination database parameters, click **Test Connectivity** next to **Database Password** to verify whether the parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the destination database parameters based on the check results. |

6.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** In this step, DTS adds the CIDR blocks of DTS servers to the whitelist of the ApsaraDB RDS for PostgreSQL instance. This ensures that the DTS servers can connect to the ApsaraDB RDS for PostgreSQL instance.

7.  Select the migration types and the objects to be migrated.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5824948951/p73234.png)

    |Setting|Description|
    |:------|:----------|
    |Select the migration types|    -   To perform only full data migration, select **Schema Migration** and **Full Data Migration**.
    -   To ensure service continuity during data migration, select **Schema Migration**, **Full Data Migration**, and **Incremental Data Migration**. In this example, select all of the three migration types.
**Note:** If **Incremental Data Migration** is not selected, do not write data to the source database during full data migration. This ensures data consistency between the source and destination databases. |
    |Select the objects to be migrated|Select objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section.

**Note:**

    -   You can select columns, tables, or schemas as the objects to be migrated.
    -   After an object is migrated to the destination database, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are migrated to the destination RDS instance. For more information, see [Object name mapping](/intl.en-US/Data Migration/Migration task management/Object name mapping.md).
    -   If you use the object name mapping feature on an object, other objects that are dependent on the object may fail to be migrated. |

8.  In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data migration task, a precheck is performed. A data migration task can be started only if it passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and perform a precheck again.
9.  After the task passes the precheck, click **Next**.

10. In the Confirm Settings dialog box, specify the **Channel Specification** parameter and select **Data Transmission Service \(Pay-As-You-Go\) Service Terms**.

11. Click **Buy and Start** to start the migration task.


## Stop the migration task

**Warning:** We recommend that you prepare a rollback solution to migrate incremental data from the destination database to the source database in real time. This allows you to minimize the negative impact of switching your workloads to the destination database. For more information, see [Switch workloads to the destination database](/intl.en-US/Data Migration/Migration task management/Switch workloads to the destination database.md). If you do not need to switch your workloads, you can perform the following steps to stop the migration task.

-   Full data migration

    Do not manually stop a task during full data migration. Otherwise, the system may fail to migrate all data. Wait until the migration task automatically ends.

-   Incremental data migration

    The task does not automatically end during incremental data migration. You must manually stop the migration task.

    1.  Wait until the task progress bar shows **Incremental Data Migration** and **The migration task is not delayed**. Then, stop writing data to the source database for a few minutes. In some cases, the progress bar shows the delay time of **incremental data migration**.
    2.  After the status of **incremental data migration** changes to **The migration task is not delayed**, manually stop the migration task.

        ![Stop a task during incremental migration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2940359951/p47604.png)


