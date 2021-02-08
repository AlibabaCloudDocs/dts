# Migrate full data from an Amazon RDS for SQL Server instance to an ApsaraDB RDS for SQL Server instance

This topic describes how to migrate full data from an Amazon RDS for SQL Server instance to an ApsaraDB RDS for SQL Server instance by using Data Transmission Service \(DTS\).

-   The **Public accessibility** option of the Amazon RDS for SQL Server instance is set to **Yes**. This ensures that DTS can access the Amazon RDS for SQL Server instance over the Internet.
-   The database version of the Amazon RDS for SQL Server instance is 2005, 2008, 2008 R2, 2012, 2014, or 2016.
-   An ApsaraDB RDS for SQL Server instance is created. For more information, see [Create an ApsaraDB RDS for SQL Server instance](https://www.alibabacloud.com/help/zh/doc-detail/53729.htm).
-   The available storage space of the ApsaraDB RDS for SQL Server instance is larger than the total size of the data in the Amazon RDS for SQL Server instance.

## Precautions

-   DTS uses read and write resources of the source and destination databases during full data migration. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you migrate data, evaluate the impact of data migration on the performance of the source and destination databases. We recommend that you migrate data during off-peak hours. For example, you can migrate data when the CPU utilization of the source and destination databases is less than 30%.
-   Amazon RDS for SQL Server does not support incremental data migration because the sysadmin role is unavailable in Amazon RDS.
-   The tables to be migrated in the source database must have PRIMARY KEY or UNIQUE constraints and all fields must be unique. Otherwise, the destination database may contain duplicate data records.
-   If the name of the source database is invalid, you must create a database in the ApsaraDB RDS for SQL Server instance before you configure a data migration task.

    **Note:** For more information about how to create a database and the database naming conventions, see [Create a database on an ApsaraDB RDS for SQL Server instance](https://www.alibabacloud.com/help/zh/doc-detail/43164.htm).

-   If a data migration task fails, DTS automatically resumes the task. Before you switch your workloads to the destination instance, stop or release the data migration task. Otherwise, the data in the source database will overwrite the data in the destination instance after the task is resumed.

## Migration types

-   Schema migration

    DTS migrates the schemas of the required objects to the destination instance. DTS supports schema migration for the following types of objects: table, view, trigger, synonym, SQL stored procedure, SQL function, plan guide, user-defined type, rule, and default.

-   Full data migration

    DTS migrates historical data of the required objects from the Amazon RDS for SQL Server instance to the ApsaraDB RDS for SQL Server instance.

    **Note:** DTS does not migrate data of the sql\_variant type.


## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|:-------------|:-------------------------|:-------------------|
|Schema migration and full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|

## Permissions required for database accounts

|Database|Schema migration|Full data migration|
|:-------|:---------------|:------------------|
|Amazon RDS SQL Server|The SELECT permission|The SELECT permission|
|ApsaraDB RDS for SQL Server|The read and write permissions|The read and write permissions|

For information about how to create and authorize a database account, see the following topics:

-   Amazon RDS for SQL Server instance: [CREATE USER](https://docs.microsoft.com/zh-cn/sql/t-sql/statements/create-user-transact-sql?view=sql-server-2017)
-   ApsaraDB RDS for SQL Server instance: [Create an account for an ApsaraDB RDS for SQL Server instance](https://www.alibabacloud.com/help/zh/doc-detail/95810.htm).

## Full data migration

To prevent data migration failures caused by dependencies among objects, DTS migrates the schemas and data of the source SQL Server database in the following order:

1.  Migrate the schemas of tables, views, synonyms, user-defined types, rules, defaults, and plan guides.

2.  Perform full data migration.

3.  Migrate the schemas of SQL stored procedures, SQL functions, triggers, and foreign keys.


## Before you begin

1.  Log on to the Amazon RDS Management Console.

2.  Go to the Basic information page of the Amazon RDS for SQL Server instance.

3.  In the Security group rules section, click the name of the security group corresponding to the existing inbound rule.

    ![Security group rules](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6820359951/p41899.png)

4.  On the Security groups page, click the Inbound tab in the Security group section. On the Inbound tab, click Edit to add the CIDR blocks of DTS servers in the corresponding region to the inbound rule. For more information, see [Add the CIDR blocks of DTS servers to the security settings of on-premises databases]().

    ![Edit inbound rules](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7334948951/p77360.png)

    **Note:**

    -   You need to add only the CIDR blocks of DTS servers that reside in the same region as the destination database. For example, the source database resides in the Singapore \(Singapore\) region and the destination database resides in the China \(Hangzhou\) region. You need to add only the CIDR blocks of DTS servers that reside in the China \(Hangzhou\) region.
    -   You can add all of the required CIDR blocks to the inbound rule at a time.

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Data Migration**.

3.  At the top of the Migration Tasks page, select the region where the destination cluster resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.

5.  Configure the source and destination databases.

    ![Configure the source and destination databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7334948951/p47832.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|Instance Type|Select **User-Created Database with Public IP Address**.|
    |Instance Region|If the instance type is set to **User-Created Database with Public IP Address**, you do not need to specify the **instance region**.|
    |Database Type|Select **SQL Server**.|
    |Hostname or IP Address|Enter the endpoint that is used to connect to the Amazon RDS for SQL Server instance. **Note:** You can obtain the endpoint on the Basic information page of the Amazon RDS for SQL Server instance.

 ![Endpoint](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6820359951/p47946.png) |
    |Port Number|Enter the service port number of the Amazon RDS for SQL Server instance. The default port number is **1433**.|
    |Database Account|Enter the database account of the Amazon RDS for SQL Server instance. For more information about permissions required for the account, see [Permissions required for database accounts](#section_fs9_4mb_sxw).|
    |Database Password|Enter the password of the source database account. **Note:** After you specify the source database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
    |Destination Database|Instance Type|Select **RDS Instance**.|
    |Instance Region|Select the region where the ApsaraDB RDS for SQL Server instance resides.|
    |RDS Instance ID|Select the ID of the ApsaraDB RDS for SQL Server instance.|
    |Database Account|Enter the database account of the ApsaraDB RDS for SQL Server instance. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_fs9_4mb_sxw).|
    |Database Password|Enter the password of the destination database account. **Note:** After you specify the destination database parameters, click **Test Connectivity** next to **Database Password** to verify whether the parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the destination database parameters based on the check results. |

6.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelist of the ApsaraDB RDS for SQL Server instance. This ensures that DTS servers can connect to the ApsaraDB RDS for SQL Server instance.

7.  Select the migration types and the objects to be migrated.

    ![Select the migration types and the objects to be migrated](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7334948951/p47833.png)

    |Setting|Description|
    |:------|:----------|
    |Select the migration types|Select **Schema Migration** and **Full Data Migration**. **Note:**

    -   Amazon RDS for SQL Server does not support incremental data migration because the sysadmin role is unavailable in Amazon RDS.
    -   To ensure data consistency, we recommend that you do not write data to the Amazon RDS for SQL Server instance during full data migration. |
    |Select the objects to be migrated|Select one or more objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section.

 **Note:**

    -   You can select columns, tables, or databases as the objects to be migrated.
    -   By default, after an object is migrated to the destination database, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are migrated to the destination database. For more information, see [Object name mapping](/intl.en-US/Data Migration/Migration task management/Object name mapping.md).
    -   If you use the object name mapping feature on an object, other objects that are dependent on the object may fail to be migrated. |

8.  In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data migration task, a precheck is performed. DTS can migrate data only if the precheck is past.
    -   If the precheck fails, click the ![Prompt](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Fix the issues as prompted and run the precheck again.
9.  After the precheck is past, click **Next**.

10. In the Confirm Settings dialog box, specify the **Channel Specification** parameter and select **Data Transmission Service \(Pay-As-You-Go\) Service Terms**.

11. Click **Buy and Start** to start the data migration task.

    **Note:** We recommend that you do not manually stop a data migration task. Otherwise, data migrated to the destination instance will be incomplete. You can wait until the data migration task automatically stops.

    ![Full data migration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7334948951/p63118.png)

12. Switch your workloads to the ApsaraDB RDS for SQL Server instance.


