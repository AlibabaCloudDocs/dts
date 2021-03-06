# Migrate data from an Amazon Aurora MySQL cluster to a PolarDB for MySQL cluster

This topic describes how to migrate data from an Amazon Aurora MySQL cluster to a PolarDB for MySQL cluster by using Data Transmission Service \(DTS\). DTS supports schema migration, full data migration, and incremental data migration. You can select all of the supported migration types to ensure service continuity.

-   The Public accessibility option of the Amazon Aurora MySQL cluster is set to **Yes**. The setting ensures that DTS can access the Amazon Aurora MySQL cluster over the Internet.
-   A PolarDB for MySQL cluster is created. For more information, see [Create a PolarDB for MySQL cluster](https://www.alibabacloud.com/help/zh/doc-detail/58769.htm).
-   The available storage space of the PolarDB for MySQL cluster is larger than the total size of the data in the Amazon Aurora MySQL cluster.

## Precautions

-   DTS uses read and write resources of the source and destination databases during full data migration. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you migrate data, evaluate the impact of data migration on the performance of the source and destination databases. We recommend that you migrate data during off-peak hours. For example, you can migrate data when the CPU utilization of the source and destination databases is less than 30%.
-   The source database must have PRIMARY KEY or UNIQUE constraints and all fields must be unique. Otherwise, the destination database may contain duplicate data records.
-   DTS uses the `ROUND(COLUMN,PRECISION)` function to retrieve values from columns of the FLOAT or DOUBLE data type. If you do not specify a precision, DTS sets the precision for the FLOAT data type to 38 digits and the precision for the DOUBLE data type to 308 digits. You must check whether the precision settings meet your business requirements.
-   If the name of the source database is invalid, you must create a database in the PolarDB for MySQL cluster before you configure a data migration task.

    **Note:** For more information about how to create a database and the database naming conventions, see [Create a database](https://www.alibabacloud.com/help/zh/doc-detail/94091.htm).

-   If a data migration task fails, DTS automatically resumes the task. Before you switch your workloads to the destination cluster, stop or release the data migration task. Otherwise, the data in the source database will overwrite the data in the destination cluster after the task is resumed.

## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|--------------|--------------------------|--------------------|
|Schema migration and full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [t216039.md\#]().|
|Incremental data migration|Charged. For more information, see [t216039.md\#]().|

## Migration types

-   Schema migration

    DTS migrates the schemas of the required objects to the PolarDB for MySQL cluster. DTS supports schema migration for the following types of objects: table, view, trigger, stored procedure, and function. DTS does not support schema migration for events.

    **Note:**

    -   During schema migration, DTS changes the value of the SECURITY attribute from DEFINER to INVOKER for views, stored procedures, and functions.
    -   DTS does not migrate user information. Before a user can call views, stored procedures, and functions of the destination database, you must grant the read and write permissions to the user.
-   Full data migration

    DTS migrates historical data of the required objects from the Amazon Aurora MySQL cluster to the PolarDB for MySQL cluster.

    **Note:** During full data migration, concurrent INSERT operations cause fragmentation in the tables of the destination cluster. After full data migration is complete, the tablespace of the destination cluster is larger than that of the source database.

-   Incremental data migration

    After full data migration is complete, DTS retrieves binary log files from the Amazon Aurora MySQL cluster. Then, DTS synchronizes incremental data from the Amazon Aurora MySQL cluster to the PolarDB for MySQL cluster. Incremental data migration allows you to ensure service continuity when you migrate data between MySQL databases.


## Permissions required for database accounts

|Database|Schema migration|Full data migration|Incremental data migration|
|:-------|:---------------|:------------------|--------------------------|
|Amazon Aurora MySQL|The SELECT permission on the objects to be migrated|The SELECT permission on the objects to be migrated|The SELECT permission on the objects to be migrated, the REPLICATION SLAVE permission, the REPLICATION CLIENT permission, and the SHOW VIEW permission|
|PolarDB for MySQL|The read and write permissions on the objects to be migrated|The read and write permissions on the objects to be migrated|The read and write permissions on the objects to be migrated|

For information about how to create and authorize a database account, see the following topics:

-   Amazon Aurora MySQL cluster: [t961261.md\#]()
-   PolarDB for MySQL: [Create a database account](https://www.alibabacloud.com/help/zh/doc-detail/68508.htm).

## Before you begin

1.  Log on to the Amazon Aurora console.

2.  Go to the Basic information page of the Amazon Aurora MySQL cluster.

3.  Select the node that assumes the **writer** role.

4.  In the Connectivity & security section, click the name of the VPC security group that corresponds to the writer node.

    ![Security group rules](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2503019951/p41943.png)

5.  On the Security groups page, click the Inbound tab in the Security group section. On the Inbound tab, click Edit to add the CIDR blocks of DTS servers in the corresponding region to the inbound rule. For more information, see [t17236.md\#]().

    ![Edit inbound rules](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6820359951/p62948.png)

    **Note:**

    -   You only need to add the CIDR blocks of DTS servers that reside in the same region as the destination database. For example, the source database resides in the Singapore \(Singapore\) region and the destination database resides in the China \(Hangzhou\) region. You only need to add the CIDR blocks of DTS servers that reside in the China \(Hangzhou\) region.
    -   You can add all of the required CIDR blocks to the inbound rule at a time.
6.  Log on to the Amazon Aurora MySQL database and specify the number of hours to retain binary log files. Skip this step if you do not need to perform incremental data migration.

    ```
    call mysql.rds_set_configuration('binlog retention hours', 24);
    ```

    **Note:**

    -   The preceding command sets the retention period of binary log files to 24 hours. The maximum value is 168 hours \(7 days\).
    -   The binary logging feature of the Amazon Aurora MySQL cluster must be enabled and the value of the binlog\_format parameter must be set to row. If the MySQL version is 5.6 or later, the value of the binlog\_row\_image parameter must be set to full.

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Data Migration**.

3.  At the top of the Migration Tasks page, select the region where the destination cluster resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.

5.  Configure the source and destination databases.

    ![Configure the source and destination databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2503019951/p40701.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|Instance Type|Select **User-Created Database with Public IP Address**.|
    |Instance Region|If the instance type is set to **User-Created Database with Public IP Address**, you do not need to specify the **instance region**.|
    |Database Type|Select **MySQL**.|
    |Hostname or IP Address|Enter the endpoint that is used to connect to the Amazon Aurora MySQL cluster. **Note:** You can obtain the endpoint on the Basic information page of the Amazon Aurora MySQL cluster.

 ![Endpoint](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2503019951/p41949.png) |
    |Port Number|Enter the service port number of the Amazon Aurora MySQL cluster. The default port number is **3306**.|
    |Database Account|Enter the database account of the Amazon Aurora MySQL cluster. For more information about the permissions that are required for the account, see [\#section\_dsb\_md9\_s9i](#section_dsb_md9_s9i).|
    |Database Password|Enter the password of the source database account. **Note:** After you specify the source database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
    |Destination Database|Instance Type|Select **PolarDB**.|
    |Instance Region|Select the region where the PolarDB for MySQL cluster resides.|
    |PolarDB Instance ID|Select the ID of the PolarDB for MySQL cluster.|
    |Database Account|Enter the database account of the PolarDB for MySQL cluster. For more information about the permissions that are required for the account, see [\#section\_dsb\_md9\_s9i](#section_dsb_md9_s9i).|
    |Database Password|Enter the password of the destination database account. **Note:** After you specify the destination database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the destination database parameters based on the check results. |

6.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** In this step, DTS adds the CIDR blocks of DTS servers to the whitelist of the PolarDB for MySQL cluster. This ensures that DTS servers can connect to the cluster.

7.  Select the migration types and the objects to be migrated.

    ![Select the migration types and the objects to be migrated](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8906544061/p47745.png)

    |Setting|Description|
    |:------|:----------|
    |Select the migration types|    -   To perform only full data migration, select **Schema Migration** and **Full Data Migration**.
    -   To ensure service continuity during data migration, select **Schema Migration**, **Full Data Migration**, and **Incremental Data Migration**.
 **Note:** If **Incremental Data Migration** is not selected, we recommend that you do not write data to the Amazon Aurora MySQL cluster during full data migration. This ensures data consistency between the source and destination databases. |
    |Select the objects to be migrated|Select one or more objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section.

 **Note:**

    -   You can select columns, tables, or databases as the objects to be migrated.
    -   By default, after an object is migrated to the destination database, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are migrated to the destination database. For more information, see [t17120.md\#](/intl.en-US/Data Migration/Migration task management/Object name mapping.md).
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

12. Switch your workloads to the PolarDB for MySQL cluster.


