# Synchronize data from an ApsaraDB RDS for MySQL instance to an AnalyticDB for MySQL cluster

AnalyticDB for MySQL is a real-time online analytical processing \(RT-OLAP\) service developed by Alibaba Cloud for online data analysis with high concurrency. AnalyticDB for MySQL can analyze petabytes of data from multiple dimensions at millisecond-level timing to provide you with data-driven insights into your business. This topic describes how to synchronize data from an ApsaraDB RDS for MySQL instance to an AnalyticDB for MySQL cluster by using Data Transmission Service \(DTS\). AnalyticDB for MySQL allows you to build internal business intelligence \(BI\) systems, interactive query systems, and real-time report systems.

-   The tables to be synchronized from the ApsaraDB RDS for MySQL instance contain primary keys.
-   An AnalyticDB for MySQL cluster is created. For more information, see [Create an AnalyticDB for MySQL cluster](https://www.alibabacloud.com/help/doc-detail/122234.htm).
-   The destination AnalyticDB for MySQL cluster has sufficient storage space.

## Precautions

-   DTS uses read and write resources of the source and destination databases during initial full data synchronization. This may increase the database load. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before synchronizing data, you must evaluate the performance of the source and destination databases. We recommend that you synchronize data during off-peak hours. For example, you can synchronize data when the CPU usage of the source and destination databases is less than 30%.
-   We recommend that you do not use gh-ost or pt-online-schema-change to perform DDL operations on objects during data synchronization. Otherwise, data synchronization may fail.
-   If the disk space usage of nodes in an AnalyticDB for MySQL cluster reaches 80%, the cluster is locked. We recommend that you estimate the required disk space based on the objects to be synchronized. You must ensure that the destination cluster has sufficient storage space.

## Supported source database types

You can use DTS to synchronize data from the following types of MySQL databases:

-   ApsaraDB RDS for MySQL
-   User-created database hosted on ECS
-   User-created database connected over Express Connect, VPN Gateway, or Smart Access Gateway

This topic uses **ApsaraDB RDS for MySQL** as an example to describe how to configure a data synchronization task. You can also follow the procedure to configure data synchronization tasks for user-created MySQL databases.

**Note:** If your source database is a user-created MySQL database, you must create a database account and configure binary logging. For more information, see [t961261.md\#]().

## SQL operations that can be synchronized

-   DDL operations: CREATE TABLE, DROP TABLE, RENAME TABLE, TRUNCATE TABLE, ADD COLUMN, and DROP COLUMN
-   DML operations: INSERT, UPDATE, and DELETE

**Note:** If the data type of a field in the source table is changed during data synchronization, an error message is generated and the data synchronization task stops. You can [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm#/ticket/createIndex) or manually troubleshoot the issue. For more information, see [\#section\_o6l\_gcd\_cqe](#section_o6l_gcd_cqe).

## Permissions required for database accounts

|Database|Required permission|
|--------|-------------------|
|ApsaraDB RDS for MySQL|The REPLICATION SLAVE permission, the REPLICATION CLIENT permission, and the permission to perform SELECT operations on the required objects|
|AnalyticDB for MySQL|The read/write permissions for the objects to be synchronized|

## Data type mapping

The data types of MySQL and AnalyticDB for MySQL do not have one-to-one correspondence. During initial schema synchronization, DTS maps the data types of the source database to the destination database. For more information, see [t1436545.md\#](/intl.en-US/Data Synchronization/Data type mappings for initial schema synchronization.md).

## Procedure

1.  Purchase a data synchronization instance. For more information, see [t17076.md\#]().

    **Note:** On the buy page, set Source Instance to **MySQL**, Target Instance to **AnalyticDB for MySQL**, and Synchronization Topology to **One-Way Synchronization**.

2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

3.  In the left-side navigation pane, click **Data Synchronization**.

4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column.

6.  Configure the source and destination instances.

    ![Configure the source and destination instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p55263.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you use an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|Select **RDS Instance**.|
    |Instance Region|The region of the source instance. The region is the same as the source region that you selected when you purchased the data synchronization instance. You cannot change the value of this parameter.|
    |Instance ID|Select the ID of the source RDS instance.|
    |Database Account|Enter the database account of the source RDS instance. For more information about permissions required for the account, see [\#section\_w01\_xz7\_hp4](#section_w01_xz7_hp4). **Note:** If the database engine of the source RDS instance is **MySQL 5.5** or **MySQL 5.6**, you do not need to configure the **database account** or **database password**. |
    |Database Password|Enter the password for the source database account.|
    |Encryption|Select **Non-encrypted** or **SSL-encrypted**. If you want to select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before configuring the data synchronization task. For more information, see [Configure SSL encryption for an RDS for MySQL instance](https://www.alibabacloud.com/help/doc-detail/96120.htm). **Note:** The **Encryption** parameter is available only in mainland China and Hong Kong\(China\). |
    |Destination Instance Details|Instance Type|The value of this parameter is set to **AnalyticDB** and cannot be changed.|
    |Instance Region|The region of the destination instance. The region is the same as the destination region that you selected when you purchased the data synchronization instance. You cannot change the value of this parameter.|
    |Version|Select **3.0**.|
    |Database|Select the ID of the destination AnalyticDB for MySQL cluster.|
    |Database Account|Enter the database account of the AnalyticDB for MySQL cluster.For more information about permissions required for the account, see [\#section\_w01\_xz7\_hp4](#section_w01_xz7_hp4).|
    |Database Password|Enter the password for the destination database account.|

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

8.  Configure the synchronization policy and objects.

    ![Configure the synchronization policy and objects](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p55267.png)

    |Parameter|Description|
    |:--------|:----------|
    |Initial Synchronization|You must select both **Initial Schema Synchronization** and **Initial Full Data Synchronization** in most cases. After the precheck, DTS synchronizes the schemas and data of the required objects from the source instance to the destination cluster. The schemas and data are the basis for subsequent incremental synchronization.|
    |Processing Mode In Existed Target Table|    -   **Pre-check and Intercept**: checks whether the destination database contains tables that have the same names as tables in the source database. If the destination database does not contain tables that have the same names as tables in the source database, the precheck is passed. Otherwise, an error is returned during precheck and the data synchronization task cannot be started.

**Note:** If tables in the destination database have the same names as tables in the source database, and cannot be deleted or renamed, you can use the object name mapping feature. For more information, see [t947854.md\#](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md).

    -   **Ignore**: skips the precheck for identical table names in the source and destination databases.

**Warning:** If you select **Ignore**, data consistency is not guaranteed and your business may be exposed to potential risks.

        -   If the source and destination databases have the same schema, DTS does not synchronize data records that have the same primary keys as data records in the destination database.
        -   If the source and destination databases have different schemas, initial data synchronization may fail. In this case, only some columns are synchronized or the data synchronization task fails. |
    |Merge Multi Tables|    -   If you select **Yes**, DTS adds the `__dts_data_source` column to each table to record data sources. In this case, DDL operations cannot be synchronized.
    -   **No** is selected by default. In this case, DDL operations can be synchronized.
 **Note:** You can merge the data source columns based on tasks rather than tables. To merge only the data source columns of some tables, you can create two data synchronization tasks. |
    |Synchronization Type|Select the types of operations that you want to synchronize based on your business requirements. All operation types are selected by default. **Note:** Only INSERT, UPDATE, DELETE, and ADD COLUMN operations can be synchronized. |
    |Objects to be synchronized|Select objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section.

 You can select tables and databases as the objects to be synchronized.

 **Note:**

    -   If you select a database as the object to be synchronized, all schema changes in the database are synchronized to the destination database.
    -   If you select a table as the object to be synchronized, only ADD COLUMN operations on the table are synchronized to the destination database.
    -   After an object is synchronized to the destination database, the name of the object remains unchanged. You can change the name of an object in the destination cluster by using the object name mapping feature. For more information about how to use this feature, see [t947854.md\#](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md). |

9.  In the lower-right corner of the page, click **Next**.

10. Specify a type for the tables to be synchronized to the destination database.

    ![Specify a table type](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p55270.png)

    **Note:** After you select **Initial Schema Synchronization**, you must specify the **type**, **primary key column**, and **partition key column** for the tables to be synchronized to AnalyticDB for MySQL. For more information, see [CREATE TABLE](https://www.alibabacloud.com/help/doc-detail/123333.htm).

11. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, a precheck is performed. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
12. Close the Precheck dialog box after the following message is displayed: **The precheck is passed.**

13. Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    You can view the status of the data synchronization task on the Data Synchronization page.

    ![Status of the data synchronization task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5130359951/p41059.png)


## Troubleshoot the synchronization failure that occurs due to field type changes

In this example, the data of a table named customer fails to be synchronized to the destination AnalyticDB for MySQL cluster.

1.  In the destination AnalyticDB for MySQL cluster, create a table named customer\_new with the same schema as the customer table.

2.  Run the INSERT INTO SELECT command to copy the data of the customer table and insert the data into the customer\_new table. This ensures that the data of the two tables is consistent.

3.  Rename or delete the customer table, and change the name of the customer\_new table to customer.

4.  Restart the data synchronization task in the DTS console.


