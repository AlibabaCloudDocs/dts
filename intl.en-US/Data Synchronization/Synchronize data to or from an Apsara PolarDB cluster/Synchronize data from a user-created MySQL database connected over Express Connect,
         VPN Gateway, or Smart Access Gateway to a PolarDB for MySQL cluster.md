# Synchronize data from a user-created MySQL database connected over Express Connect, VPN Gateway, or Smart Access Gateway to a PolarDB for MySQL cluster

PolarDB is a next-generation relational database service that is developed by Alibaba Cloud. PolarDB is compatible with the MySQL database engine and features high availability, ease of use, and reliability. This topic describes how to synchronize data from a user-created MySQL database that is connected over Express Connect, VPN Gateway, or Smart Access Gateway to a PolarDB for MySQL cluster by using Data Transmission Service \(DTS\).

-   A MySQL database of version 5.1, 5.5, 5.6, 5.7, or 8.0 is created.
-   The user-created MySQL database is connected to Alibaba Cloud VPC over Express Connect, VPN Gateway, or Smart Access Gateway. For more information, see [t1853322.md\#]().

    **Note:** DTS is allowed to access the VPC to which the user-created MySQL database belongs. For more information, see [t190285.md\#]().

-   A PolarDB for MySQL cluster is created. For more information, see [Create a PolarDB for MySQL cluster](https://www.alibabacloud.com/help/zh/doc-detail/58769.htm)

    **Note:** The available storage space of the PolarDB for MySQL cluster is larger than the total size of the data in the user-created MySQL database.


## Precautions

-   DTS uses read and write resources of the source and destination databases during initial full data synchronization. This may increase the database load. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before synchronizing data, you must evaluate the performance of the source and destination databases. We recommend that you synchronize data during off-peak hours. For example, you can synchronize data when the CPU usage of the source and destination databases is less than 30%.
-   If you have selected one or more tables \(not a database\) for synchronization, do not use gh-ost or pt-online-schema-change to modify the tables during data synchronization. Otherwise, data synchronization may fail.

    **Note:** To avoid synchronization failure, you can use Data Management \(DMS\) to perform online DDL schema changes during data synchronization. For more information, see [Change the table schema without locking](https://www.alibabacloud.com/help/doc-detail/98373.htm).

-   During initial full data synchronization, concurrent INSERT operations cause fragmentation in the tables of the destination cluster. After initial full data synchronization, the tablespace of the destination cluster is larger than that of the source database.
-   The source database must have PRIMARY KEY or UNIQUE constraints and all fields must be unique. Otherwise, duplicate data may exist in the destination cluster.

## SQL operations that can be synchronized

|Operation type|SQL statements|
|--------------|--------------|
|DML|INSERT, UPDATE, DELETE, and REPLACE|
|DDL|-   ALTER TABLE and ALTER VIEW
-   CREATE FUNCTION, CREATE INDEX, CREATE PROCEDURE, CREATE TABLE, and CREATE VIEW
-   DROP INDEX and DROP TABLE
-   RENAME TABLE
-   TRUNCATE TABLE |

## Supported synchronization topologies

-   One-way one-to-one synchronization
-   One-way one-to-many synchronization
-   One-way cascade synchronization
-   One-way many-to-one synchronization

For more information about synchronization topologies, see [t790489.md\#](/intl.en-US/Data Synchronization/Synchronization topologies.md).

## Limits

-   Incompatibility with triggers

    If the object you want to synchronize is a database and the database contains a trigger that updates the synchronized table, the synchronized data may be inconsistent. For example, the source database contains Table A and Table B. If a data record is inserted into Table A, a trigger inserts a data record into Table B. In this case, after an INSERT operation is performed on Table A in the source database, the data in Table B becomes inconsistent between the source and destination databases.

    To avoid this situation, you must delete the trigger that is synchronized to the destination database and select Table B as the object to be synchronized. For more information, see [Configure synchronization when triggers exist](https://www.alibabacloud.com/help/doc-detail/26655.htm).

-   Limits on RENAME TABLE operations

    RENAME TABLE operations may cause data inconsistency between the source and destination databases. For example, if only Table A needs to be synchronized and it is renamed Table B, Table B cannot be synchronized to the destination database. To avoid this situation, you can select the database to which Table A and Table B belong as the object when configuring the data synchronization task.


## Before you begin

[t961261.md\#]()

**Note:** The database account must have the SELECT permission on the objects to be synchronized, the REPLICATION CLIENT permission, the REPLICATION SLAVE permission, and the SHOW VIEW permission.

## Procedure

1.  Purchase a data synchronization instance. For more information, see [t17076.md\#]().

    **Note:** On the buy page, set Source Instance to **MySQL**, set Target Instance to **PolarDB**, and set Synchronization Topology to **One-Way Synchronization**.

2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

3.  In the left-side navigation pane, click **Data Synchronization**.

4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column.

6.  Configure the source and destination instances.

    ![Configure the source and destination instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0803019951/p66907.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|Select **User-Created Database Connected over Express Connect, VPN Gateway, or Smart Access Gateway**.|
    |Instance Region|The source region that you selected on the buy page. You cannot change the value of this parameter.|
    |Peer VPC|Select the ID of the VPC that is connected to the user-created MySQL database.|
    |Database Type|The value of this parameter is set to **MySQL** and cannot be changed.|
    |IP Address|Enter the server IP address of the user-created MySQL database.|
    |Port Number|Enter the service port number of the user-created MySQL database.|
    |Database Account|Enter the account that you created for the user-created MySQL database. For more information, see [\#section\_0ag\_b7q\_lj5](#section_0ag_b7q_lj5).|
    |Database Password|Enter the password of the source database account.|
    |Destination Instance Details|Instance Type|The value of this parameter is set to **PolarDB** and cannot be changed.|
    |Instance Region|The destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |PolarDB Instance ID|Select the ID of the destination PolarDB cluster.|
    |Database Account|Enter the database account of the destination PolarDB cluster. **Note:** The database account must have the ALL permission on the objects to be synchronized. |
    |Database Password|Enter the password of the destination database account.|

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelist of the destination PolarDB cluster. This ensures that DTS servers can connect to the destination PolarDB cluster.

8.  Configure the processing mode in existing destination tables and the objects to be synchronized.

    ![Configure the processing mode and objects](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4108539951/p54325.png)

    |Parameter|Description|
    |:--------|:----------|
    |Processing Mode In Existed Target Table|    -   **Pre-check and Intercept**: checks whether the destination database contains tables that have the same names as tables in the source database. If the destination database does not contain tables that have the same names as tables in the source database, the precheck is passed. Otherwise, an error is returned during precheck and the data synchronization task cannot be started.

**Note:** If tables in the destination database have the same names as tables in the source database, and cannot be deleted or renamed, you can use the object name mapping feature. For more information, see [t947854.md\#](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md).

    -   **Ignore**: skips the precheck for identical table names in the source and destination databases.

**Warning:** If you select **Ignore**, data consistency is not guaranteed and your business may be exposed to potential risks.

        -   DTS does not synchronize data records that have the same primary keys as data records in the destination database during initial data synchronization. This occurs if the source and destination databases have the same schema. However, DTS synchronizes these data records during incremental data synchronization.
        -   If the source and destination databases have different schemas, initial data synchronization may fail. In this case, only some columns are synchronized or the data synchronization task fails. |
    |Objects|Select objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section.

 You can select tables and databases as the objects to be synchronized.

 **Note:**

    -   If you select a database as the object to be synchronized, all schema changes in the database are synchronized to the destination database.
    -   After an object is synchronized to the destination database, the name of the object remains unchanged. You can change the name of an object in the destination PolarDB cluster by using the object name mapping feature. For more information about how to use this feature, see [t947854.md\#](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md). |

9.  In the lower-right corner of the page, click **Next**.

10. Configure initial synchronization.

    ![Advanced settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8030359951/p41055.png)

    **Note:** Initial synchronization includes initial schema synchronization and initial full data synchronization. Select both **Initial Schema Synchronization** and **Initial Full Data Synchronization**. Before synchronizing incremental data, DTS synchronizes the schemas and historical data of the required objects from the source database to the destination database.

11. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, a precheck is performed. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
12. Close the Precheck dialog box after the following message is displayed: **The precheck is passed**.

13. Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    On the Synchronization Tasks page, view the status of the data synchronization task.

    ![View the status of a data synchronization task.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5130359951/p41059.png)


