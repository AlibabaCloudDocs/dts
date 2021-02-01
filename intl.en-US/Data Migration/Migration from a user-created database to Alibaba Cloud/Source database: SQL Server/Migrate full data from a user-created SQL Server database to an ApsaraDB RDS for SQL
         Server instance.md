# Migrate full data from a user-created SQL Server database to an ApsaraDB RDS for SQL Server instance

This topic describes how to migrate full data from a user-created SQL Server database to an ApsaraDB RDS for SQL Server instance by using Data Transmission Service \(DTS\). DTS supports schema migration, full data migration, and incremental data migration. To migrate full data from a user-created SQL Server database, you can select schema migration and full data migration.

**Note:** For information about how to migrate data without service disruptions, see [Migrate incremental data from a user-created SQL Server database to an ApsaraDB RDS for SQL Server instance](/intl.en-US/Data Migration/Migration from a user-created database to Alibaba Cloud/Source database: SQL Server/Migrate incremental data from a user-created SQL Server database to an ApsaraDB RDS
         for SQL Server instance.md).

## Prerequisites

-   The version of the user-created SQL Server database is 2005, 2008, 2008 R2, 2012, 2014, 2016, or 2017.

    **Note:**

    -   If a primary/secondary switchover is performed in a SQL Server cluster or Always On availability group \(AOAG\), the log serial numbers of the primary database and the secondary database become inconsistent. In this case, DTS considers that the source database does not support resumable transmission, and the migration task fails. Therefore, you cannot use a SQL Server cluster or a SQL Server AOAG as the source database.
    -   If you migrate data between different versions of databases, make sure that the database versions are compatible.
-   The available storage space of the ApsaraDB RDS for SQL Server instance is larger than the total size of the data in the user-created SQL Server database.

## Precautions

-   DTS uses read and write resources of the source and destination databases during full data migration. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you migrate data, evaluate the impact of data migration on the performance of the source and destination databases. We recommend that you migrate data during off-peak hours. For example, you can migrate data when the CPU utilization of the source and destination databases is less than 30%.
-   The tables to be migrated in the source database must have PRIMARY KEY or UNIQUE constraints and all fields must be unique. Otherwise, the destination database may contain duplicate data records.
-   DTS automatically creates a destination database in the ApsaraDB RDS for SQL Server instance. However, if the name of the source database is invalid, you must create a database in the ApsaraDB RDS for SQL Server instance before you configure the data migration task.

    **Note:** For more information about how to create a database and the database naming conventions, see [Create a database on an ApsaraDB RDS for SQL Server instance](https://www.alibabacloud.com/help/zh/doc-detail/95698.htm).

-   If a data migration task fails, DTS automatically resumes the task. Before you switch your workloads to the destination instance, stop or release the data migration task. Otherwise, the data in the source database will overwrite the data in the destination instance after the task is resumed.

## Limits

-   DTS does not migrate data of the sql\_variant type.
-   DTS does not migrate the schemas of assemblies, service brokers, full-text indexes, full-text catalogs, distributed schemas, distributed functions, CLR stored procedures, CLR scalar-valued functions, CLR table-valued functions, internal tables, systems, or aggregate functions.

## Migration types

-   Schema migration

    DTS migrates the schemas of the required objects to the destination instance. DTS supports schema migration for the following types of objects: table, view, trigger, synonym, SQL stored procedure, SQL function, plan guide, user-defined type, rule, and default.

-   Full data migration

    DTS migrates historical data of the required objects from the user-created SQL Server database to the destination database in the ApsaraDB RDS for SQL Server instance.


## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|:-------------|:-------------------------|:-------------------|
|Full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|

## Permissions required for database accounts

|Database|Schema migration|Full data migration|
|:-------|:---------------|:------------------|
|User-created SQL Server database|The SELECT permission|The SELECT permission|
|ApsaraDB RDS for SQL Server instance|The read and write permissions|The read and write permissions|

For information about how to create and authorize a database account, see the following topics:

-   User-created SQL Server database: [CREATE USER](https://docs.microsoft.com/zh-cn/sql/t-sql/statements/create-user-transact-sql?view=sql-server-2017)
-   ApsaraDB RDS for SQL Server instance: [Create an account for an ApsaraDB RDS for SQL Server instance](https://www.alibabacloud.com/help/zh/doc-detail/95810.htm).

## Full data migration

To prevent data migration failures caused by dependencies among objects, DTS migrates the schemas and data of the source SQL Server database in the following order:

1.  Migrate the schemas of tables, views, synonyms, user-defined types, rules, defaults, and plan guides.
2.  Perform full data migration.
3.  Migrate the schemas of SQL stored procedures, SQL functions, triggers, and foreign keys.

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).
2.  In the left-side navigation pane, click **Data Migration**.
3.  At the top of the Migration Tasks page, select the region where the destination RDS instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.
5.  Configure the source and destination databases.

    ![Configure the source and destination databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7334948951/p47832.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|Instance Type|Select an instance type based on the deployment of the source database. In this example, select **User-Created Database with Public IP Address**. **Note:** If you select other instance types, you must prepare the environment that is required for the source database. For more information, see [Preparation overview](). |
    |Instance Region|If the instance type is set to **User-Created Database with Public IP Address**, you do not need to specify the **instance region**. **Note:** If a whitelist is configured for the user-created SQL Server database, you must add the CIDR blocks of DTS servers to the whitelist of the database. You can click **Get IP Address Segment of DTS** next to **Instance Region** to obtain the CIDR blocks of DTS servers. |
    |Database Type|Select **SQL Server**.|
    |Hostname or IP Address|Enter the endpoint that is used to connect to the user-created SQL Server database. In this example, enter the public IP address.|
    |Port Number|Enter the service port number of the user-created SQL Server database. The default port number is **1433**.**Note:** The service port of the user-created SQL Server database must be accessible over the Internet. |
    |Database Account|Enter the account that is used to log on to the user-created SQL Server database. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_j7q_aei_kt6).|
    |Database Password|Enter the password of the source database account. **Note:** After you specify the source database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
    |Destination Database|Instance Type|Select **RDS Instance**.|
    |Instance Region|Select the region where the destination RDS instance resides.|
    |RDS Instance ID|Select the ID of the destination RDS instance.|
    |Database Account|Enter the database account of the destination RDS instance. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_j7q_aei_kt6).|
    |Database Password|Enter the password of the destination database account. **Note:** After you specify the destination database parameters, click **Test Connectivity** next to **Database Password** to verify whether the parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the destination database parameters based on the check results. |

6.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelist of the destination RDS instance. This ensures that DTS servers can connect to the destination RDS instance.

7.  Select the migration types and the objects to be migrated.

    ![Select the migration types and the objects to be migrated](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7334948951/p47833.png)

    |Setting|Description|
    |:------|:----------|
    |Select the migration types|    -   To perform only full data migration, select **Schema Migration** and **Full Data Migration**.
    -   To ensure service continuity during data migration, select **Schema Migration**, **Full Data Migration**, and **Incremental Data Migration**.
In this example, select **Schema Migration** and **Full Data Migration**.

**Note:** To ensure data consistency, we recommend that you do not write data to the user-created SQL Server database during full data migration. |
    |Select the objects to be migrated|Select objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section.

**Note:**

    -   You can select columns, tables, or databases as the objects to be migrated.
    -   By default, after an object is migrated to the destination database, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are migrated to the destination database. For more information, see [Object name mapping](/intl.en-US/Data Migration/Migration task management/Object name mapping.md).
    -   If you use the object name mapping feature on an object, other objects that are dependent on the object may fail to be migrated. |

8.  In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data migration task, a precheck is performed. You can start the data migration task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
9.  After the task passes the precheck, click **Next**.
10. In the Confirm Settings dialog box, specify the **Channel Specification** and select **Data Transmission Service \(Pay-As-You-Go\) Service Terms**.
11. Click **Buy and Start** to start the data migration task.

    **Note:** We recommend that you do not manually stop a data migration task. Otherwise, data migrated to the destination instance will be incomplete. You can wait until the data migration task automatically stops.

12. Switch your workloads to the destination RDS instance.

## What to do next

The database accounts that are used for data migration have the read and write permissions. After data migration is complete, you must delete the accounts of both the user-created SQL Server database and the ApsaraDB RDS for SQL Server instance to ensure security.

