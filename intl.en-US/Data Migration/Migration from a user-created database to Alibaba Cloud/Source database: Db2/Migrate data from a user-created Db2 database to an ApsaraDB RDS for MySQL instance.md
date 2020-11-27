# Migrate data from a user-created Db2 database to an ApsaraDB RDS for MySQL instance

This topic describes how to migrate data from a user-created Db2 database to an ApsaraDB RDS for MySQL instance by using Data Transmission Service \(DTS\). DTS supports schema migration, full data migration, and incremental data migration. When you migrate data from a user-created Db2 database, you can select all of the supported migration types to ensure service continuity.

## Prerequisites

-   The version of the Db2 database is 9.7 or 10.5.
-   The available storage space of the destination ApsaraDB RDS for MySQL instance is larger than the total size of the data in the user-created Db2 database.

## Precautions

-   DTS does not synchronize data definition language \(DDL\) operations to the destination instance.
-   If the name of the source database is invalid, you must create a database in the ApsaraDB RDS for MySQL instance before you configure a data migration task.

    **Note:** For more information about how to create a database and the database naming conventions, see [Create a database on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96105.htm).

-   DTS uses read and write resources of the source and destination databases during full data migration. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you migrate data, evaluate the impact of data migration on the performance of the source and destination databases. We recommend that you migrate data during off-peak hours. For example, you can migrate data when the CPU utilization of the source and destination databases is less than 30%.
-   If a data migration task fails, DTS automatically resumes the task. Before you switch your workloads to the destination instance, stop or release the data migration task. Otherwise, the data in the source database will overwrite the data in the destination instance after the task is resumed.

## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|--------------|--------------------------|--------------------|
|Schema migration and full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|
|Incremental data migration|Charged. For more information, see [Pricing]().|

## Migration types

-   Schema migration

    DTS migrates the schemas of the required objects to the destination instance. DTS supports schema migration for the following types of objects: table, index, and foreign key.

-   Full data migration

    DTS migrates historical data of the required objects from the source Db2 database to the destination ApsaraDB RDS for MySQL instance.

-   Incremental data migration

    After full data migration is complete, DTS synchronizes incremental data from the source Db2 database to the destination ApsaraDB RDS for MySQL instance. Incremental data migration allows you to ensure service continuity when you migrate data from a user-created Db2 database.


## Permissions required for database accounts

|Database|Schema migration|Full data migration|Incremental data migration|
|:-------|:---------------|:------------------|:-------------------------|
|Db2 database|The CONNECT and SELECT permissions|The CONNECT and SELECT permissions|The DBADM permission|
|ApsaraDB RDS for MySQL instance|The read and write permissions|The read and write permissions|The read and write permissions|

For information about how to create and authorize a database account, see the following topics:

-   Db2 database: [Creating group and user IDs for a Db2 database installation \(Linux and UNIX\)](https://www.ibm.com/support/knowledgecenter/zh/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0006742.html#t0006742) and [Authorities overview](https://www.ibm.com/support/knowledgecenter/zh/SSEPGG_11.1.0/com.ibm.db2.luw.admin.sec.doc/doc/c0055206.html)
-   ApsaraDB RDS for MySQL instance: [Create an account on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96089.htm) and [Modify the permissions of a standard account for an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96101.htm).

## Data migration process

To avoid data migration failures caused by dependencies between objects, DTS migrates the schemas and data of the source Db2 database in the following order:

1.  Migrate the schemas and indexes.
2.  Perform full data migration.
3.  Migrate the schemas of foreign keys.
4.  Perform incremental data migration.

## Before you begin

Before you configure an incremental data migration task, enable the archive log feature for the source Db2 database. For more information, see [Primary log archive method](https://www.ibm.com/support/knowledgecenter/SSEPGG_10.5.0/com.ibm.db2.luw.admin.config.doc/doc/r0011448.html) and [Secondary log archive method](https://www.ibm.com/support/knowledgecenter/SSEPGG_10.5.0/com.ibm.db2.luw.admin.config.doc/doc/r0011449.html).

**Note:** Skip this step if you perform only full data migration.

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).
2.  In the left-side navigation pane, click **Data Migration**.
3.  At the top of the Migration Tasks page, select the region where the destination RDS instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.
5.  Configure the **source and destination databases**.

    ![Configure the source and destination databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2820359951/p64320.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|Instance Type|Select an instance type based on the deployment of the source database. In this example, select **User-Created Database with Public IP Address**. **Note:** If you select other instance types, you must prepare the environment that is required for the source database. For more information, see [Preparation overview](). |
    |Instance Region|If the instance type is set to **User-Created Database with Public IP Address**, you do not need to specify the **instance region**. **Note:** If a whitelist is configured for the user-created Db2 database, you must add the CIDR blocks of DTS servers to the whitelist of the database. You can click **Get IP Address Segment of DTS** next to **Instance Region** to obtain the CIDR blocks of DTS servers. |
    |Database Type|Select **DB2**.|
    |Hostname or IP Address|Enter the endpoint that is used to connect to the user-created Db2 database. In this example, enter the public IP address.|
    |Port Number|Enter the service port number of the user-created Db2 database. The default port number is **50000**. **Note:** The service port of the user-created Db2 database must be accessible over the Internet. |
    |Database Name|Enter the name of the user-created Db2 database.|
    |Database Account|Enter the account of the user-created Db2 database. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_bjn_5zq_5hb).|
    |Database Password|Enter the password of the source database account. **Note:** After you specify the source database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
    |Destination Database|Instance Type|Select **RDS Instance**.|
    |Instance Region|Select the region where the destination RDS instance resides.|
    |RDS Instance ID|Select the ID of the destination RDS instance.|
    |Database Account|Enter the database account of the destination RDS instance. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_bjn_5zq_5hb).|
    |Database Password|Enter the password of the destination database account. **Note:** After you specify the destination database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the destination database parameters based on the check results. |
    |Encryption|Select **Non-encrypted** or **SSL-encrypted**. If you want to select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before you configure the data migration task. For more information, see [Configure SSL encryption on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96120.htm) **Note:** The **Encryption** parameter is available only for regions in mainland China and the China \(Hong Kong\) region. |

6.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelist of the destination RDS instance. This ensures that DTS servers can connect to the destination RDS instance.

7.  Select the migration types and the objects to be migrated.

    ![Select the migration types and the objects to be migrated](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8906544061/p47745.png)

    |Setting|Description|
    |:------|:----------|
    |Select the migration types|    -   To perform only full data migration, select **Schema Migration** and **Full Data Migration**.
    -   To ensure service continuity during data migration, select **Schema Migration**, **Full Data Migration**, and **Incremental Data Migration**.
**Note:** If **Incremental Data Migration** is not selected, do not write data to the user-created Db2 database during full data migration. This ensures data consistency between the source and destination databases. |
    |Select the objects to be migrated|Select objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section.

**Note:**

    -   You can select columns, tables, or databases as the objects to be migrated.
    -   After an object is migrated to the destination RDS instance, the name of the object remains the same as that in the source Db2 database. You can use the object name mapping feature to change the names of the objects that are migrated to the destination RDS instance. For more information, see [Object name mapping](/intl.en-US/Data Migration/Migration task management/Object name mapping.md).
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

12. Switch your workloads to the ApsaraDB RDS for MySQL instance.

