# Migrate full data from a user-created PostgreSQL database to an ApsaraDB RDS for PostgreSQL instance

This topic describes how to migrate full data from a user-created PostgreSQL database to an ApsaraDB RDS for PostgreSQL instance by using Data Transmission Service \(DTS\). DTS supports schema migration, full data migration, and incremental data migration. To migrate full data from a user-created PostgreSQL database, you can select schema migration and full data migration when you configure a task.

## Background information

This topic uses **User-Created Database with Public IP Address** as an example to describe how to configure a full data migration task. To ensure data consistency, we recommend that you do not write data to the user-created PostgreSQL database during full data migration. For information about how to migrate data with minimal downtime, see [Migrate incremental data from a user-created PostgreSQL database \(version 10.1 to 12\) to an ApsaraDB RDS for PostgreSQL instance](/intl.en-US/Data Migration/Migration from a user-created database to Alibaba Cloud/Source database: PostgreSQL/Migrate incremental data from a user-created PostgreSQL database (version 10.1 to 12) to an ApsaraDB RDS for PostgreSQL instance.md) and [Migrate incremental data from a user-created PostgreSQL database \(versions 9.4 to 10.0\) to an ApsaraDB RDS for PostgreSQL instance](/intl.en-US/Data Migration/Migration from a user-created database to Alibaba Cloud/Source database: PostgreSQL/Migrate incremental data from a user-created PostgreSQL database (versions 9.4 to
         10.0) to an ApsaraDB RDS for PostgreSQL instance.md).

## Prerequisites

-   A PostgreSQL database of version 9.2, 9.3, 9.4, 9.5, 9.6, 10.x, 11, or 12 is created.
-   The available storage space of the ApsaraDB RDS for PostgreSQL instance is larger than the total size of the data in the user-created PostgreSQL database.
-   The service port of the user-created PostgreSQL database is accessible over the Internet.

## Precautions

-   DTS uses read and write resources of the source and destination databases during full data migration. This may increase the load of the database server. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you migrate data, evaluate the impact of data migration on the performance of the source and destination databases. We recommend that you migrate data during off-peak hours. For example, you can migrate data when the CPU utilization of the source and destination databases is less than 30%.
-   The name of the source database cannot contain hyphens \(-\), for example, dts-testdata.
-   The tables to be migrated in the source database must have PRIMARY KEY or UNIQUE constraints and all fields must be unique. Otherwise, the destination database may contain duplicate data records.
-   A single data migration task can migrate data from only one database. To migrate data from multiple databases, you must create a data migration task for each database.
-   If a data migration task fails, DTS automatically resumes the task. Before you switch your workloads to the destination instance, stop or release the data migration task. Otherwise, the data in the source database will overwrite the data in the destination instance after the task is resumed.

## Migration types

-   Schema migration

    DTS migrates the schemas of the required objects to the destination instance. DTS supports schema migration for the following types of objects: table, trigger, view, sequence, function, user-defined type, rule, domain, operation, and aggregate.

-   Full data migration

    DTS migrates historical data of the required objects from the user-created PostgreSQL database to the destination ApsaraDB RDS for PostgreSQL instance.


## Billing

|Migration type|Instance configurations|Internet traffic|
|:-------------|:----------------------|:---------------|
|Full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|

## Permissions required for database accounts

|Database|Schema migration|Full data migration|
|:-------|:---------------|:------------------|
|User-created PostgreSQL database|The usage permission on pg\_catalog|The SELECT permission on the objects to be migrated|
|ApsaraDB RDS for PostgreSQL instance|The create and usage permissions on the objects to be migrated|The owner permission on schemas|

For more information about how to create and authorize a database account, see the following topics:

-   User-created PostgreSQL database: [CREATE USER](https://www.postgresql.org/docs/10/sql-createuser.html) and [GRANT](https://www.postgresql.org/docs/10/sql-grant.html)
-   ApsaraDB RDS for PostgreSQL instance: [Create an account for an ApsaraDB RDS for PostgreSQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96753.htm)

## Process of full data migration

To avoid data migration failures caused by dependencies between objects, DTS migrates the schemas and data of the source PostgreSQL database in the following order:

1.  Migrate the schemas of tables, views, sequences, functions, user-defined types, rules, domains, operations, and aggregates.

    **Note:** Functions that are written in the C programming language cannot be migrated.

2.  Migrate full data.
3.  Migrate the schemas of triggers and foreign keys.

## Before you begin

Create a database and schema in the destination RDS instance based on the database and schema information of the objects to be migrated. The schema name of the source and destination databases must be the same. For more information, see [Create a database on an ApsaraDB RDS for PostgreSQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96758.htm) and [Appendix: User and schema management](https://www.alibabacloud.com/help/zh/doc-detail/26159.htm).

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).
2.  In the left-side navigation pane, click **Data Migration**.
3.  At the top of the Migration Tasks page, select the region where the destination RDS instance resides.

    ![Select a region](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.
5.  Configure the source and destination databases.

    ![Configure the source and destination databases](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1924948951/p47957.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|Instance Type|Select an instance type based on the deployment of the source database. In this example, select **User-Created Database with Public IP Address**. **Note:** If you select other instance types, you must prepare the environment that is required for the source database. For more information, see [Preparation overview](). |
    |Instance Region|If the instance type is set to **User-Created Database with Public IP Address**, you do not need to specify the **instance region**. **Note:** If a whitelist is configured for the user-created PostgreSQL database, you must add the CIDR blocks of DTS servers to the whitelist of the database. You can click **Get IP Address Segment of DTS** next to **Instance Region** to obtain the CIDR blocks of DTS servers. |
    |Database Type|Select **PostgreSQL**.|
    |Hostname or IP Address|Enter the endpoint that is used to connect to the user-created PostgreSQL database. In this example, enter the public IP address.|
    |Port Number|Enter the service port number of the user-created PostgreSQL database. The default port number is **5432**.|
    |Database Name|Enter the name of the source PostgreSQL database.|
    |Database Account|Enter the account that is used to log on to the user-created PostgreSQL database. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_0u5_y38_oi7).|
    |Database Password|Enter the password of the source database account. **Note:** After you specify the source database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
    |Destination Database|Instance Type|Select **RDS Instance**.|
    |Instance Region|Select the region where the destination RDS instance resides.|
    |RDS Instance ID|Select the ID of the destination RDS instance.|
    |Database Name|Enter the name of the destination database in the RDS instance. The name can be different from the name of the source PostgreSQL database. **Note:** Before you configure the data migration task, create a database and schema in the destination RDS instance. For more information, see [Before you begin](#section_96j_36l_ytf). |
    |Database Account|Enter the database account of the destination RDS instance. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_0u5_y38_oi7).|
    |Database Password|Enter the password of the destination database account. **Note:** After you specify the destination database parameters, click **Test Connectivity** next to **Database Password** to verify whether the parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the destination database parameters based on the check results. |

6.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelist of the destination RDS instance. This ensures that DTS servers can connect to the destination RDS instance.

7.  Select the migration types and the objects to be migrated.

    ![Select the migration types and the objects to be migrated](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4334948951/p47963.png)

    |Parameter|Description|
    |:--------|:----------|
    |Migration Types|    -   To perform only full data migration, select **Schema Migration** and **Full Data Migration**.
    -   To ensure service continuity during data migration, select **Schema Migration**, **Full Data Migration**, and **Incremental Data Migration**.
In this example, you must select **Schema Migration** and **Full Data Migration**.

**Note:** To ensure data consistency, we recommend that you do not write data to the user-created PostgreSQL database during full data migration. |
    |Objects|Select objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to add the objects to the Selected section.

**Note:**

    -   You can select columns, tables, or schemas as the objects to be migrated.
    -   After an object is migrated to the destination database, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are migrated to the destination database. For more information, see [Object name mapping](/intl.en-US/Data Migration/Migration task management/Object name mapping.md).
    -   If you use the object name mapping feature on an object, other objects that are dependent on the object may fail to be migrated. |

8.  In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data migration task, a precheck is performed. You can start the data migration task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
9.  After the task passes the precheck, click **Next**.
10. In the Confirm Settings dialog box, specify the **Channel Specification** and select **Data Transmission Service \(Pay-As-You-Go\) Service Terms**.
11. Click **Buy and Start** to start the data migration task.

    **Note:** Do not manually stop a task during full data migration. Otherwise, data migrated to the destination database will be incomplete. Wait until the data migration task automatically stops.

12. Switch your workloads to the destination RDS instance.

## What to do next

The database accounts that are used for data migration have the read and write permissions. After data migration is complete, you must delete the accounts of both the user-created PostgreSQL database and the ApsaraDB RDS for PostgreSQL instance to ensure security.

