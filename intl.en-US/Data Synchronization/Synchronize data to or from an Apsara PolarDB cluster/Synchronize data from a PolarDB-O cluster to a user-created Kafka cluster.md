# Synchronize data from a PolarDB-O cluster to a user-created Kafka cluster

Kafka is a distributed message queue service that features high throughput and high scalability. Kafka is widely used in big data fields such as log collection, monitoring data aggregation, streaming data processing, and online and offline analysis. It is indispensable for the big data ecosystem. This topic describes how to synchronize data from an ApsaraDB PolarDB-O Cluster instance to a user-created Kafka cluster by using Data Transmission Service \(DTS\). This allows you to extend message processing capabilities.

-   The source PolarDB-O cluster uses the latest version. For more information, see [Upgrade the minor version]().
-   The tables to be synchronized contain primary keys or UNIQUE NOT NULL indexes.
-   The value of the wal\_level parameter is set to logical for the source PolarDB-O cluster. This setting ensures that logical decoding is supported in write-ahead logging \(WAL\). For more information, see [Change the values of cluster parameters]().

## Precautions

-   In this scenario, DTS only supports the , not supported and .
-   A data synchronization task can synchronize only one database. To synchronize data from multiple databases, you must create a data synchronization task for each database.
-   To ensure the accuracy of incremental data migration, DTS adds a heartbeat table to the user-created SQL Server database. The table name is \_dts\_mysql\_heartbeat.
-   DTS adds a new table named `dts_postgres_heartbeat` as shown in the following figure.

    ![Schema](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2310398951/p94992.png)




## Procedure

1.  Purchase a data synchronization instance. For more information, see [Purchase a DTS instance]().

    **Note:** When purchasing, set Source Instance to**PolarDB**, set Target Instance to **Kafka**, and setSynchronization Topology to **One-Way Synchronization**.

2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

3.  In the left-side navigation pane, click **Data Synchronization**.

4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column.

6.  Configure the source and destination instances.

    ![Configure the source and destination instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2310398951/p101774.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|The value of this parameter is set to **PolarDB Instance** and cannot be changed.|
    |Instance Region|The region of the source instance. The region is the same as the source region that you selected when you purchased the data synchronization instance. You cannot change the value of this parameter.|
    |PolarDB Instance ID|Select the ID of the source PolarDB cluster.|
    |Database Name|Enter the name of the source database.|
    |Database Account|Enter the account of the source PolarDB cluster. For more information about how to create a database account, see [Create database accounts]().|
    |Database Password|Enter the password for the database account.|
    |Destination Instance Details|Instance Type|Select based on the deployment location of the user-created Kafka. The procedure in this topic uses **user-created database hosted on ECS** as an example. **Note:** If you select other instance types, you must prepare the environments that are required for the database. For more information, see [Preparation overview](). |
    |Instance Region|The region of the destination instance. The region is the same as the destination region that you selected when you purchased the data synchronization instance. You cannot change the value of this parameter.|
    |ECS Instance ID|Select the ID of the ECS instance on which the Kafka cluster is deployed.|
    |Database Type|Select **Kafka**.|
    |Port Number|Enter the port number of the Kafka cluster, The default port number is 9092.|
    |Database Account|Enter the username that is used to log on to the Kafka cluster. If the authentication is disabled for the Kafka cluster, you do not need to enter the username.|
    |Database Password|Enter the password for the username. If the authentication is disabled for the Kafka cluster, you do not need to enter the password.|
    |Topic|Click **Get Topic list**, then select a specific Topic from the drop-down list.|
    |Kafka Version|Select a version based on the version of the destination Kafka cluster.|
    |Encryption|Select **Non-encrypted** or **SCRAM-SHA-256** based on your business and security requirements.|

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** The CIDR blocks of DTS servers are automatically added to the whitelist of the PolarDB cluster and the inbound rule of the ECS instance. This ensures that DTS servers can connect to the source cluster and destination instance.

8.  Configure the objects to be synchronized.

    ![Select synchronization objects](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2310398951/p101776.png)

    |Parameter|Description|
    |:--------|:----------|
    |Objects to be synchronized|Select tables from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the tables to the Selected section. **Note:** DTS automatically maps the table names to the topic name that you select when you configure the source and destination instances. If you want to change the destination topic, you can move the pointer over the table and click **Edit** . You must specify a topic that exists in the Message Queue for Apache Kafka instance. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md). |

9.  In the lower-right corner of the page, click **Next**.

10. Configure initial synchronization and filter options.

    ![Advanced configurations for data synchronization](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2310398951/p101781.png)

    |Parameter|Description|
    |---------|-----------|
    |Initial Synchronization|**Initial incremental data synchronization** is selected by default. DTS synchronizes incremental data that is generated in the source database to the destination database in real time.|
    |Filter options|**Ignore DDL statements in the incremental synchronization phase** is selected by default. DTS does not synchronize DDL operations that are performed on the source database during incremental data synchronization.|

11. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, a precheck is performed. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
12. Close the Precheck dialog box after the following message is displayed: **The precheck is passed**.

13. Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    On the Synchronization Tasks page, view the status of the data synchronization task.

    ![View the status of a data synchronization task.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5130359951/p41059.png)


