# Synchronize data from an ApsaraDB RDS for MySQL instance to an AnalyticDB for MySQL cluster

is a real-time online analytical processing \(RT-OLAP\) service that is developed by Alibaba Cloud for online data analysis with high concurrency. AnalyticDB for MySQL can analyze petabytes of data from multiple dimensions at millisecond-level timing to provide you with data-driven insights into your business. This topic describes how to synchronize data from an ApsaraDB RDS for MySQL instance to an cluster by using Data Transmission Service \(DTS\). After you synchronize data, you can use AnalyticDB for MySQL to build internal business intelligence \(BI\) systems, interactive query systems, and real-time report systems.

-   The tables that you want to synchronize from the ApsaraDB RDS for MySQL instance contain primary keys.
-   An cluster is created. For more information, see [Create an cluster](https://www.alibabacloud.com/help/zh/doc-detail/122234.htm).
-   The destination cluster has sufficient storage space.

## Precautions

-   DTS uses read and write resources of the source and destination databases during initial full data synchronization. This may increase the database load. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before synchronizing data, you must evaluate the performance of the source and destination databases. We recommend that you synchronize data during off-peak hours. For example, you can synchronize data when the CPU usage of the source and destination databases is less than 30%.
-   We recommend that you do not use gh-ost or pt-online-schema-change to perform data definition language \(DDL\) operations on the required objects during data synchronization. Otherwise, data may fail to be synchronized.
-   Due to the limits of , if the disk space usage of the nodes in an cluster reaches 80%, the cluster is locked. We recommend that you estimate the required disk space based on the objects that you want to synchronize. You must ensure that the destination cluster has sufficient storage space.
-   Prefix indexes cannot be synchronized. If the source database contains prefix indexes, data may fail to be synchronized.

## SQL operations that can be synchronized

-   DDL operations: CREATE TABLE, DROP TABLE, RENAME TABLE, TRUNCATE TABLE, ADD COLUMN, DROP COLUMN, and MODIFY COLUMN
-   Data manipulation language \(DML\) operations: INSERT, UPDATE, and DELETE

**Note:** If the data type of a field in the source table is changed during data synchronization, an error message is generated and the data synchronization task is stopped. You can [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm#/ticket/createIndex) or troubleshoot the issue. For more information, see [Troubleshoot the synchronization failure that occurs due to field type changes](#section_o6l_gcd_cqe).

## Permissions required for database accounts

|Database|Required permissions|
|--------|--------------------|
|ApsaraDB RDS for MySQL|The database account must have the SELECT permission on the objects to be synchronized, the REPLICATION CLIENT permission, the REPLICATION SLAVE permission, and the SHOW VIEW permission.|
| |The read and write permissions on the objects to be synchronized.|

## Data type mappings

The data types of ApsaraDB RDS for MySQL and do not have one-to-one correspondence. During initial schema synchronization, DTS converts the data types of the source database into those of the destination database. For more information, see [Data type mappings for initial schema synchronization](/intl.en-US/Data Synchronization/Data type mappings for initial schema synchronization.md).

## Procedure

1.  Purchase a data synchronization instance. For more information, see [Purchase procedure]().

    **Note:** On the buy page, set Source Instance to **MySQL**, set Target Instance to **AnalyticDB MySQL**, and set Synchronization Topology to **One-Way Synchronization**.

2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

3.  In the left-side navigation pane, click **Data Synchronization**.

4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column.

6.  Configure the source and destination instances.

    ![Configure the source and destination instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p55263.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|Select **RDS Instance**.|
    |Instance Region|The region of the source instance. The region is the same as the source region that you selected on the buy page. You cannot change the value of this parameter.|
    |Instance ID|Select the ID of the source RDS instance.|
    |Database Account|Enter the database account of the source RDS instance. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_w01_xz7_hp4). **Note:** If the database engine of the source RDS instance is **MySQL 5.5** or **MySQL 5.6**, you do not need to configure the **database account** or **database password**. |
    |Database Password|Enter the password of the source database account.|
    |Encryption|Select **Non-encrypted** or **SSL-encrypted**. If you select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before you configure the data synchronization task. For more information, see [Configure SSL encryption on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96120.htm). **Note:** The **Encryption** parameter is available only for regions in mainland China and the Hong Kong \(China\) region. |
    |Destination Instance Details|Instance Type|The value of this parameter is set to **AnalyticDB** and cannot be changed.|
    |Instance Region|The region of the destination cluster. The region is the same as the destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |Version|Select **3.0**.|
    |Database|Select the ID of the destination cluster.|
    |Database Account|Enter the database account of the destination cluster.For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_w01_xz7_hp4).|
    |Database Password|Enter the password of the destination database account.|

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

8.  Select the synchronization policy and the objects to be synchronized.

    ![Select the synchronization policy and the objects to be synchronized](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p55267.png)

    |Parameter|Description|
    |:--------|:----------|
    |Initial Synchronization|You must select both **Initial Schema Synchronization** and **Initial Full Data Synchronization** in most cases. After the precheck, DTS synchronizes the schemas and data of the required objects from the source instance to the destination cluster. The schemas and data are the basis for subsequent incremental synchronization.|
    |Processing Mode In Existed Target Table|    -   **Pre-check and Intercept**: checks whether the destination database contains tables that have the same names as tables in the source database. If the source and destination databases do not contain identical table names, the precheck is passed. Otherwise, an error is returned during precheck and the data synchronization task cannot be started.

**Note:** You can use the object name mapping feature to change the names of the tables that are synchronized to the destination database. You can use this feature if the source and destination databases contain identical table names and the tables in the destination database cannot be deleted or renamed. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md).

    -   **Ignore**: skips the precheck for identical table names in the source and destination databases.

**Warning:** If you select **Ignore**, data consistency is not guaranteed and your business may be exposed to potential risks.

        -   If the source and destination databases have the same schema, DTS does not synchronize data records that have the same primary keys as data records in the destination database.
        -   If the source and destination databases have different schemas, initial data synchronization may fail. In this case, only specific columns are synchronized or the data synchronization task fails. |
    |Merge Multi Tables|    -   If you select **Yes**, DTS adds the `__dts_data_source` column to each table to record data sources. In this case, DDL operations cannot be synchronized.
    -   **No** is selected by default. In this case, DDL operations can be synchronized.
**Note:** You can merge the data source columns based on tasks rather than tables. To merge only the data source columns of specific tables, you can create two data synchronization tasks. |
    |Synchronization Type|Select the types of operations that you want to synchronize based on your business requirements. All operation types are selected by default. For more information, see [SQL operations that can be synchronized](#section_s1c_w58_ncx).|
    |Select the objects to be synchronized|Select objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section.

You can select tables or databases as the objects to be synchronized.

**Note:**

    -   If you select a database as the object to be synchronized, all schema changes in the database are synchronized to the destination database.
    -   If you select a table as the object to be synchronized, only the ADD COLUMN operations that are performed on the table are synchronized to the destination database.
    -   After an object is synchronized to the destination cluster, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are synchronized to the destination cluster. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md). |

9.  In the lower-right corner of the page, click **Next**.

10. Specify a type for the tables that you want to synchronize to the destination database.

    ![Specify a table type](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p55270.png)

    **Note:** After you select **Initial Schema Synchronization**, you must specify the **type**, **primary key column**, and **partition key column** for the tables that you want to synchronize to . For more information, see [CREATE TABLE](https://www.alibabacloud.com/help/zh/doc-detail/123333.htm).

11. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, a precheck is performed. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
12. Close the Precheck dialog box after the following message is displayed: **The precheck is passed.**

13. Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    You can view the status of the data synchronization task on the Data Synchronization page.

    ![Status of the data synchronization task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5130359951/p41059.png)


## Troubleshoot the synchronization failure that occurs due to field type changes

In this example, the data of a table named customer fails to be synchronized to the cluster.

1.  In the cluster, create a table named customer\_new. The customer\_new table has the same schema as the customer table.

2.  Run the INSERT INTO SELECT command to copy the data of the customer table and insert the data into the customer\_new table. This ensures that the data of the two tables is consistent.

3.  Rename or delete the customer table. Then, change the name of the customer\_new table to customer.

4.  Restart the data synchronization task in the DTS console.


