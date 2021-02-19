# Configure two-way data synchronization between ApsaraDB RDS for MySQL instances

Data Transmission Service \(DTS\) supports real-time two-way data synchronization between two MySQL databases. This feature is applicable to scenarios such as active geo-redundancy \(unit-based\) and geo-disaster recovery. This topic describes how to configure two-way data synchronization between ApsaraDB RDS for MySQL instances. You can also follow the procedure to configure data synchronization tasks for user-created MySQL databases.

## Prerequisites

The source and destination ApsaraDB RDS for MySQL instances are created. For more information, see [Create an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/26117.htm).

## Precautions

-   DTS uses read and write resources of the source and destination databases during initial full data synchronization. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you synchronize data, evaluate the impact of data synchronization on the performance of the source and destination databases. We recommend that you synchronize data during off-peak hours. For example, you can synchronize data when the CPU utilization of the source and destination databases is less than 30%.
-   If you select one or more tables instead of an entire database as the objects to be synchronized, do not use gh-ost or pt-online-schema-change to perform data definition language \(DDL\) operations on the tables during data synchronization. Otherwise, data may fail to be synchronized.
-   If you use only DTS to write data to the destination database, you can use Data Management \(DMS\) to perform online DDL operations during data synchronization. For more information, see [Change schemas without locking tables](https://www.alibabacloud.com/help/zh/doc-detail/98373.htm).

    **Warning:** If you use tools other than DTS to write data to the destination database, we recommend that you do not use DMS to perform online DDL operations. Otherwise, data loss may occur in the destination database.


## Supported synchronization topology

DTS supports two-way data synchronization only between two MySQL databases. DTS does not support two-way data synchronization between multiple MySQL databases.

![Two-way data synchronization](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7920359951/p41047.png)

## Supported databases

The following table lists the types of MySQL databases that are supported by two-way data synchronization. This topic uses ApsaraDB RDS for MySQL instances as the data sources. You can also follow the procedure to configure two-way data synchronization for other types of MySQL databases.

|Source database|Destination database|
|:--------------|:-------------------|
|-   ApsaraDB RDS for MySQL instance
-   User-created database that is hosted on Elastic Compute Service \(ECS\)
-   User-created database that is connected over Express Connect, VPN Gateway, or Smart Access Gateway
-   User-created database that is connected over Database Gateway
-   User-created database that is connected over Cloud Enterprise Network \(CEN\)

|-   ApsaraDB RDS for MySQL instance
-   User-created database that is hosted on Elastic Compute Service \(ECS\)
-   User-created database that is connected over Express Connect, VPN Gateway, or Smart Access Gateway
-   User-created database that is connected over Database Gateway
-   User-created database that is connected over Cloud Enterprise Network \(CEN\) |

## SQL operations that can be synchronized

|Operation type|SQL statements|
|--------------|--------------|
|DML|INSERT, UPDATE, DELETE, and REPLACE|
|DDL|-   ALTER TABLE and ALTER VIEW
-   CREATE FUNCTION, CREATE INDEX, CREATE PROCEDURE, CREATE TABLE, and CREATE VIEW
-   DROP INDEX and DROP TABLE
-   RENAME TABLE
-   TRUNCATE TABLE |

## Conflict detection

To ensure data consistency, make sure that data records with the same primary key, business primary key, or unique key are updated only on one of the synchronization nodes. If data records are updated on both nodes, DTS responds to conflicts based on the conflict resolution policy that you specify for the data synchronization task.

DTS checks and fixes conflicts to maximize the stability of two-way synchronization instances. DTS can detect the following types of conflicts:

-   Uniqueness conflicts caused by INSERT operations

    INSERT operations that do not comply with the uniqueness constraint cannot be synchronized. For example, if a record with the same primary key value is inserted into the two synchronization nodes at almost the same time, one of the inserted records fails to be synchronized. The synchronization fails because a record with the same primary key value already exists in the other node.

-   Inconsistent records caused by UPDATE operations
    -   If the records to be updated do not exist in the destination instance, DTS converts the UPDATE operation into an INSERT operation. However, uniqueness conflicts may occur.
    -   The primary keys or unique keys of the records to be inserted may conflict with those of existing records in the destination instance.
-   Non-existent records to be deleted

    The records to be deleted do not exist in the destination instance. In this case, DTS ignores the DELETE operation regardless of the conflict resolution policy that you specify.


**Note:**

-   During two-way synchronization, the system time of the source and destination instances may be different. Synchronization latency may occur. For these reasons, DTS cannot guarantee that the conflict detection mechanism can prevent all data conflicts. To perform two-way synchronization, make sure that records with the same primary key, business primary key, or unique key are updated only on one of the synchronization nodes.
-   DTS provides conflict resolution policies to prevent conflicts that may occur during data synchronization. You can select a conflict resolution policy when you configure two-way data synchronization.

## Limits

-   Incompatibility with triggers

    If you select a database as the object to be synchronized and the database contains a trigger that updates a table, data inconsistency may occur. For example, the source database contains Table A and Table B. If a data record is inserted into Table A, a trigger inserts a data record into Table B. In this case, after an INSERT operation is performed on Table A in the source instance, the data in Table B becomes inconsistent between the source and destination instances.

    To prevent this situation, before you synchronize data in Table B from the source instance, delete the trigger that is synchronized to the destination instance. For more information, see [Configure a data synchronization task for a source database that contains a trigger](/intl.en-US/Best Practices/Configure a data synchronization task for a source database that contains a trigger.md).

-   Limits on RENAME TABLE operations

    RENAME TABLE operations may cause data inconsistency between the source and destination databases. For example, if you select a table as the object and rename the table during data synchronization, the data of this table is not synchronized to the destination database. To prevent this situation, you can select the database to which this table belongs as the object when you configure the data synchronization task.

-   Limits on DDL synchronization direction

    To ensure the stability of two-way data synchronization, you can synchronize the DDL operations on a single table only in one direction. If DDL synchronization in a direction is configured, DDL synchronization in the opposite direction is not supported. Only data manipulation language \(DML\) operations can be synchronized in the opposite direction.


## Procedure

1.  Purchase an instance for two-way data synchronization. For more information, see [Purchase a data synchronization instance]().

    **Note:** On the buy page, set both Source Instance and Target Instance to **MySQL** and set Synchronization Topology to **Two-Way Synchronization**.

2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).
3.  In the left-side navigation pane, click **Data Synchronization**.
4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column of the first data synchronization task.

    **Note:** A two-way data synchronization instance contains two data synchronization tasks. You must set parameters for each task.

    ![Two-way data synchronization tasks](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7920359951/p41151.png)

6.  Configure the source and destination instances.

    ![Configure the source and destination instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7920359951/p41052.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|Select **RDS Instance**.|
    |Instance Region|The source region that you selected on the buy page. You cannot change the value of this parameter.|
    |Instance ID|Select the ID of the source RDS instance.|
    |Database Account|Enter the database account of the source RDS instance. **Note:** If the database engine of the source RDS instance is **MySQL 5.5** or **MySQL 5.6**, you do not need to configure the **database account** or **database password**. |
    |Database Password|Enter the password of the source database account.|
    |Encryption|Select **Non-encrypted** or **SSL-encrypted**. If you want to select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before you configure the data synchronization task. For more information, see [Configure SSL encryption on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96120.htm). **Note:** The **Encryption** parameter is available only for regions in mainland China and the China \(Hong Kong\) region. |
    |Destination Instance Details|Instance Type|Select **RDS Instance**.|
    |Instance Region|The destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |Instance ID|Select the ID of the destination RDS instance.|
    |Database Account|Enter the database account of the destination RDS instance. **Note:** If the database engine of the destination RDS instance is **MySQL 5.5** or **MySQL 5.6**, you do not need to configure the **database account** or **database password**. |
    |Database Password|Enter the password of the destination database account.|
    |Encryption|Select **Non-encrypted** or **SSL-encrypted**. If you want to select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before you configure the data synchronization task. For more information, see [Configure SSL encryption on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96120.htm). **Note:** The **Encryption** parameter is available only for regions in mainland China and the China \(Hong Kong\) region. |

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.
8.  Select the synchronization policy and the objects to be synchronized.

    ![Select the synchronization policy and the objects to be synchronized](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7920359951/p41053.png)

    |Setting|Parameter|Description|
    |:------|:--------|:----------|
    |Select the synchronization policy|Exclude DDL Statements|    -   To exclude DDL operations, select **Yes**.
    -   To include DDL operations, select **No**.

**Note:** To ensure the stability of two-way data synchronization, you can synchronize DDL operations only in one direction. |
    |DML Statements for Synchronization|Select the types of DML operations that you want to synchronize. By default, the **INSERT**, **UPDATE**, and **DELETE** operations are selected. You can select the DML operation types based on your business requirements.|
    |Conflict Resolution Policy|Select the resolution policy for synchronization conflicts. By default, **TaskFailed** is selected. You can select a conflict resolution policy based on your business requirements.     -   **TaskFailed**

The default conflict resolution policy. If a conflict occurs during data synchronization, the synchronization task reports an error and exits the process. The task enters a failed state and you must manually resolve the conflict.

    -   **Ignore**

If a conflict occurs during data synchronization, the synchronization task ignores the current statement and continues the process. The conflicting records in the destination database are used.

    -   **Overwrite**

If a conflict occurs during data synchronization, the conflicting records in the destination database are overwritten. |
    |Processing Mode In Existed Target Table|    -   **Pre-check and Intercept**: checks whether the destination database contains tables that have the same names as tables in the source database. If the source and destination databases do not contain identical table names, the precheck is passed. Otherwise, an error is returned during precheck and the data synchronization task cannot be started.

**Note:** You can use the object name mapping feature to change the names of the tables that are synchronized to the destination database. You can use this feature if the source and destination databases contain identical table names and the tables in the destination database cannot be deleted or renamed. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md).

    -   **Ignore**: skips the precheck for identical table names in the source and destination databases.

**Warning:** If you select **Ignore**, data consistency is not guaranteed and your business may be exposed to potential risks.

        -   During initial data synchronization, DTS does not synchronize the data records that have the same primary keys as the data records in the destination database. This occurs if the source and destination databases have the same schema. However, DTS synchronizes these data records during incremental data synchronization.
        -   If the source and destination databases have different schemas, initial data synchronization may fail. In this case, only some columns are synchronized or the data synchronization task fails. |
    |Select the objects to be synchronized|N/A|Select objects \(tables or a database\) from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section.

**Note:**

    -   If you select a database as the object to be synchronized, all schema changes in the database are synchronized to the destination database.
    -   By default, after an object is synchronized to the destination instance, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are synchronized to the destination database. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md). |
    |Specify the retry time for failed connection to the source or destination database|N/A|By default, if DTS fails to connect to the source or destination database, DTS retries within the next 12 hours. You can specify the retry time based on your needs. If DTS reconnects to the source and destination databases within the specified time, DTS resumes the data synchronization task. Otherwise, the data synchronization task fails.**Note:** When DTS retries a connection, you are charged for the DTS instance. We recommend that you specify the retry time based on your business needs. You can also release the DTS instance at your earliest opportunity after the source and destination instances are released. |

9.  In the lower-right corner of the page, click **Next**.
10. Configure initial synchronization.

    ![Advanced settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8030359951/p41055.png)

    During initial synchronization, DTS synchronizes the schemas and data of the required objects from the source instance to the destination instance. The schemas and data are the basis for subsequent incremental synchronization. Initial synchronization includes **initial schema synchronization** and **initial full data synchronization**. You must select both **Initial Schema Synchronization** and **Initial Full Data Synchronization** in most cases.

    **Note:** If tables to be synchronized in one direction are also included in the objects to be synchronized in the opposite direction, DTS does not synchronize these tables during initial synchronization.

11. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, a precheck is performed. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, you can click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details.
        -   You can troubleshoot the issues based on the causes and run a precheck again.
        -   If you do not need to troubleshoot the issues, you can ignore failed items and run a precheck again.********
12. Close the Precheck dialog box after the following message is displayed: **The precheck is passed.** Then, the data synchronization task starts.
13. Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    You can view the status of the data synchronization task on the Synchronization Tasks page.

14. Find the second data synchronization task and click **Configure Synchronization Channel** in the Actions column. Configure the task by following Steps 5 to 12.

    ![Configure data synchronization in the opposite direction](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8920359951/p41152.png)

15. After the second data synchronization task is configured, wait until both tasks are in the **Synchronizing** state. The two-way data synchronization tasks are configured.

    ![Status of two-way data synchronization tasks](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8920359951/p41154.png)


