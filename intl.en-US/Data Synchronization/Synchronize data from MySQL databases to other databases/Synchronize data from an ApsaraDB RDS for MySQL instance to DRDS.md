# Synchronize data from an ApsaraDB RDS for MySQL instance to DRDS

Distributed Relational Database Service \(DRDS\) is developed by Alibaba Group to address the bottleneck of single-host database services. DRDS is compatible with the MySQL protocol and syntax, and supports automatic sharding, online smooth scaling, auto scaling, and transparent read/write splitting. DRDS provides O&M capabilities throughout the lifecycle of databases. This topic describes how to synchronize data from an ApsaraDB RDS for MySQL instance to a DRDS instance by using Data Transmission Service \(DTS\).

## Prerequisites

-   The tables to be synchronized from the source database contain primary keys.
-   The destination database has sufficient storage space.
-   A Distributed Relational Database Service \(DRDS\) instance is created. For more information, see [t1824821.md\#]() and [t1824853.md\#]().

    **Note:** When you create a DRDS instance, you must select RDS MySQL as the storage type.


## Precautions

-   DTS uses read and write resources of the source and destination databases during initial full data synchronization. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you synchronize data, evaluate the impact of data synchronization on the performance of the source and destination databases. We recommend that you synchronize data during off-peak hours. For example, you can synchronize data when the CPU utilization of the source and destination databases is less than 30%.
-   During initial full data synchronization, concurrent INSERT operations cause fragmentation in the tables of the destination cluster. After initial full data synchronization, the tablespace of the destination cluster is larger than that of the source database. You must make sure that the destination database has sufficient storage space.
-   DTS does not synchronize schemas from an ApsaraDB RDS for MySQL instance to a DRDS instance. Before you configure a data synchronization task, you must create databases and tables in the destination instance.

## Limits

-   You can select only tables as the objects to be synchronized.
-   DTS does not synchronize the following types of data: BIT, VARBIT, GEOMETRY, ARRAY, UUID, TSQUERY, TSVECTOR, and TXID\_SNAPSHOT.
-   Prefix indexes cannot be synchronized. If the source database contains prefix indexes, data may fail to be synchronized.
-   We recommend that you do not use gh-ost or pt-online-schema-change to perform data definition language \(DDL\) operations on objects during data synchronization. Otherwise, data synchronization may fail.

## SQL operations that can be synchronized

INSERT, UPDATE, and DELETE

## Permissions required for database accounts

|Database|Required permissions|
|--------|--------------------|
|ApsaraDB RDS for MySQL|The database account must have the SELECT permission on the objects to be synchronized, the REPLICATION CLIENT permission, the REPLICATION SLAVE permission, and the SHOW VIEW permission.|
|DRDS|DTS automatically creates a database account and grants permissions to the account. You do not need to specify the database account.|

## Supported synchronization topologies

-   One-way one-to-one synchronization
-   One-way many-to-one synchronization

## Before you begin

When you synchronize data from an ApsaraDB RDS for MySQL instance to a DRDS instance, note that DTS does not support **initial schema synchronization**. Therefore, you must create databases and tables in the destination instance based on the schemas of the objects in the source ApsaraDB RDS for MySQL instance. For more information, see [Create a DRDS database](https://www.alibabacloud.com/help/zh/doc-detail/50070.htm) and [Create a DRDS table](https://www.alibabacloud.com/help/zh/doc-detail/50084.htm).

**Note:** During **initial schema synchronization**, DTS synchronizes the schemas of the required objects from the source database to the destination database.

## Procedure

1.  Purchase a data synchronization instance. For more information, see [Purchase a DTS instance]().

    **Note:** On the buy page, set Source Instance to **MySQL**, set Target Instance to **DRDS**, and set Synchronization Topology to **One-Way Synchronization**.

2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

3.  In the left-side navigation pane, click **Data Synchronization**.

4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column.

6.  Configure the source and destination instances.

    ![Configure the source and destination databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7618715061/p170872.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|Select **RDS Instance**.|
    |Instance Region|The region of the source instance. The region is the same as the source region that you selected on the buy page. You cannot change the value of this parameter.|
    |Instance ID|Select the ID of the source ApsaraDB RDS for MySQL instance.|
    |Database Account|Enter the database account of the source RDS instance. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_huh_2sf_ser). **Note:** If the database engine of the source RDS instance is **MySQL 5.5** or **MySQL 5.6**, you do not need to configure the **database account** or **database password**. |
    |Database Password|Enter the password of the source database account.|
    |Encryption|Select **Non-encrypted** or **SSL-encrypted**. If you want to select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before you configure the data synchronization task. For more information, see [Configure SSL encryption on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96120.htm). **Note:** The **Encryption** parameter is available only for regions in mainland China and the China \(Hong Kong\) region. |
    |Destination Instance Details|Instance Type|This parameter is set to **DRDS Instance** and cannot be changed.|
    |Instance Region|The region of the destination instance. The region is the same as the destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |DRDS Instance ID|Select the ID of the destination DRDS instance.|

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelists of the source and destination instances. This ensures that DTS servers can connect to the source and destination instances.

8.  Select the synchronization policy and the objects to be synchronized.

    ![Select the objects to be synchronized](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7618715061/p171518.png)

    |Setting|Description|
    |:------|:----------|
    |Select the objects to be synchronized|Select tables from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the tables to the Selected section.

**Note:**

    -   You can select only tables as the objects to be synchronized.
    -   After an object is synchronized to the destination instance, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are synchronized to the destination instance. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md). |

9.  Click **Next**.

10. Specify whether you want to perform initial full data synchronization.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5189903061/p60676.png)

    **Note:** During **initial full data synchronization**, DTS synchronizes the historical data of the required objects from the source database to the destination database. If you do not select Initial Full Data Synchronization, DTS does not synchronize the historical data.

11. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, DTS performs a precheck. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run a precheck again.
12. Close the Precheck dialog box after the following message is displayed: **The precheck is passed**.

13. Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    On the Synchronization Tasks page, view the status of the data synchronization task.

    ![View the status of a data synchronization task.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5130359951/p41059.png)


