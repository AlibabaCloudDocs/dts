# Migrate full data from an Amazon RDS for Oracle instance to an ApsaraDB RDS for PPAS instance

This topic describes how to migrate full data from an Amazon RDS for Oracle instance to an ApsaraDB RDS for PPAS instance by using Data Transmission Service \(DTS\).

-   The **Public accessibility** option of the Amazon RDS for Oracle instance is set to **Yes**. The setting ensures that DTS can access the Amazon RDS for Oracle instance over the Internet.
-   The database version of the Amazon RDS for Oracle instance is 9i, 10g, or 11g.
-   An ApsaraDB RDS for PPAS instance is created. For more information, see [Create an ApsaraDB RDS for PPAS instance](https://www.alibabacloud.com/help/zh/doc-detail/53731.htm).
-   The available storage space of the ApsaraDB RDS for PPAS instance is larger than the total size of the data in the Amazon RDS for Oracle instance.

## Precautions

-   DTS uses read and write resources of the source and destination databases during full data migration. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you migrate data, evaluate the impact of data migration on the performance of the source and destination databases. We recommend that you migrate data during off-peak hours. For example, you can migrate data when the CPU utilization of the source and destination databases is less than 30%.
-   The tables to be migrated in the source database must have PRIMARY KEY or UNIQUE constraints and all fields must be unique. Otherwise, the destination database may contain duplicate data records.
-   If a data migration task fails, DTS attempts to resume the task. Before you switch your workloads to the destination instance, stop or release the data migration task. Otherwise, the data in the source database will overwrite the data in the destination instance after the task is resumed.

## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|:-------------|:-------------------------|:-------------------|
|Schema migration and full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|

## Permissions required for database accounts

|Database|Schema migration|Full data migration|Incremental data migration|
|:-------|:---------------|:------------------|:-------------------------|
|Amazon RDS for Oracle|The owner permission on schemas|The owner permission on schemas|The permissions for the master user|
|ApsaraDB RDS for PPAS|The read and write permissions on the destination database|The read and write permissions on the destination database|The read and write permissions on the destination database|

For more information about how to create and authorize database account, see the following topics:

-   Amazon RDS for Oracle instance: [CREATE USER](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_8003.htm) and [GRANT](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_9013.htm)
-   ApsaraDB RDS for PPAS instance: [Create an account on an ApsaraDB RDS for PPAS instance](https://www.alibabacloud.com/help/zh/doc-detail/96933.htm).

## Limits

-   Amazon RDS for Oracle does not support incremental data migration because the dbcreator and sysadmin roles are unavailable in Amazon RDS.
-   DTS does not synchronize data definition language \(DDL\) operations. We recommend that you do not perform DDL operations on the source database during full data migration.
-   DTS does not migrate materialized views.
-   During schema migration, reverse indexes and bitmap indexes are converted into common indexes. Partitioned indexes are converted into independent indexes on each partition in the ApsaraDB RDS for PPAS instance.

## Data type mappings

For more information, see [Data type mappings between heterogeneous databases](/intl.en-US/Data Migration/Data type mappings between heterogeneous databases.md).

## Before you begin

1.  Log on to the Amazon RDS Management Console.

2.  Go to the Basic Information page of the source Amazon RDS for Oracle instance.

3.  In the Security group rules section, click the name of the security group corresponding to the existing inbound rule.

    ![Security group rules](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6820359951/p41899.png)

4.  On the Security Groups page, click the Inbound tab in the Security Group section. On the Inbound tab, click Edit, and add CIDR blocks of DTS servers to the Edit inbound rules dialog box that appears. The CIDR blocks of DTS servers vary with the region where the destination database resides. For more information about the CIDR blocks of DTS servers, see [Add the CIDR blocks of DTS servers to the security settings of on-premises databases]().

    ![Edit inbound rules](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8134948951/p63148.png)

    **Note:**

    -   You only need to add the CIDR blocks of DTS servers that reside in the same region as the destination database. For example, the source database resides in the Singapore \(Singapore\) region and the destination database resides in the China \(Hangzhou\) region. You only need to add the CIDR blocks of DTS servers that reside in the China \(Hangzhou\) region.
    -   You can add all of the required CIDR blocks to the inbound rule at a time.

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Data Migration**.

3.  At the top of the Migration Tasks page, select the region where the destination cluster resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.

5.  Configure the source and destination databases.

    ![Configure the source and destination databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8134948951/p57893.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|Instance Type|Select **User-Created Database with Public IP Address**.|
    |Instance Region|If the instance type is set to **User-Created Database with Public IP Address**, you do not need to specify the **instance region**.|
    |Database Type|Select **Oracle**.|
    |Hostname or IP Address|Enter the endpoint that is used to connect to the Amazon RDS for Oracle instance. **Note:** You can obtain the endpoint on the Basic information page of the Amazon RDS for Oracle instance.

![Endpoint](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6820359951/p47946.png) |
    |Port Number|Enter the service port number of the Amazon RDS for Oracle instance. The default port number is **1521**.|
    |Instance Type|    -   **Non-RAC Instance**: If you select this option, you must specify the **SID**.
    -   **RAC Instance**: If you select this option, you must specify the **Service Name**. |
    |Database Account|Enter the database account of the Amazon RDS for Oracle instance. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_vzp_y6d_9fj).|
    |Database Password|Enter the password of the source database account. **Note:** After you specify the source database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
    |Destination Database|Instance Type|Select **RDS Instance**.|
    |Instance Region|Select the region where the ApsaraDB RDS for PPAS instance resides.|
    |RDS Instance ID|Select the ID of the ApsaraDB RDS for PPAS instance.|
    |Database Name|Enter the name of the destination database.|
    |Database Account|Enter the database account of the ApsaraDB RDS for PPAS instance. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_vzp_y6d_9fj).|
    |Database Password|Enter the password of the destination database account. **Note:** After you specify the destination database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the destination database parameters based on the check results. |

6.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelist of the ApsaraDB RDS for PPAS instance. This ensures that DTS servers can connect to the ApsaraDB RDS for PPAS instance.

7.  Select the migration types and the objects to be migrated.

    ![Select the migration types and the objects to be migrated](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8134948951/p62979.png)

    |Setting|Description|
    |:------|:----------|
    |Select the migration types|Select **Schema Migration** and **Full Data Migration**. **Note:**

    -   Amazon RDS for Oracle does not support incremental data migration because the dbcreator and sysadmin roles are unavailable in Amazon RDS.
    -   To ensure data consistency, we recommend that you do not write data to the Amazon RDS for Oracle instance during full data migration. |
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

12. Switch your workloads to the ApsaraDB RDS for PPAS instance.


