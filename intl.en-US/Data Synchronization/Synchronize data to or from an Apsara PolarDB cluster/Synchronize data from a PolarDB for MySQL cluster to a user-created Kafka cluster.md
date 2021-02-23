# Synchronize data from a PolarDB for MySQL cluster to a user-created Kafka cluster

Kafka is a distributed message queue service that features high throughput and high scalability. Kafka is widely used for big data analytics such as log collection, data aggregation, streaming processing, and online and offline analysis. It is important for the big data ecosystem. This topic describes how to synchronize data from a PolarDB for MySQL cluster to a user-created Kafka cluster by using Data Transmission Service \(DTS\). The data synchronization feature allows you to extend message processing capabilities.

-   A Kafka cluster is created and the Kafka version is 0.10.1.0 to 1.0.2.
-   The binary logging feature is enabled for the PolarDB for MySQL cluster. For more information, see [Enable binary logging](https://www.alibabacloud.com/help/zh/doc-detail/113546.htm)

## Precautions

The source database must have PRIMARY KEY or UNIQUE constraints and all fields must be unique. Otherwise, the destination database may contain duplicate data records.

## Limits

-   You can select only tables as the objects to be synchronized.
-   DTS does not automatically update the objects to be synchronized based on their names.

    **Note:** DTS does not synchronize the data in a renamed table to the destination Kafka cluster. This applies if the new table name is not included in the objects to be synchronized. To synchronize the data in a renamed table to the destination Kafka cluster, you must modify the objects to be synchronized. For more information, see [Add an object to a data synchronization task](/intl.en-US/Data Synchronization/Synchronization task management/Add an object to a data synchronization task.md).


## Data format

The data that is synchronized to the Kafka cluster is stored in the Avro format. For more information, see [DTS Avro schema](https://github.com/LioRoger/subscribe_example/tree/master/avro).

**Note:** After the data is synchronized to the Kafka cluster, you must parse the data based on the Avro schema.

## Procedure

1.  Purchase a data synchronization instance. For more information, see [Purchase a DTS instance]().

    **Note:** On the buy page, set Source Instance to **PolarDB**, set Target Instance to **Kafka**, and set Synchronization Topology to **One-Way Synchronization**.

2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

3.  In the left-side navigation pane, click **Data Synchronization**.

4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column.

6.  Configure the source and destination instances.

    ![Configure the source and destination instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6703019951/p57788.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|The value of this parameter is set to **PolarDB Instance** and cannot be changed.|
    |Instance Region|The region of the source cluster. The region is the same as the source region that you selected on the buy page. You cannot change the value of this parameter.|
    |POLARDB Instance ID|Select the ID of the source PolarDB cluster.|
    |Database Account|Enter the database account of the source PolarDB cluster. The account must have the read permission on the objects to be synchronized.|
    |Database Password|Enter the password of the source database account.|
    |Destination Instance Details|Instance Type|Select an instance type based on the deployment of the Kafka cluster. In this example, select **User-Created Database in ECS Instance**.**Note:** If you select other instance types, you must prepare the environment that is required for the Kafka cluster. For more information, see [Preparation overview](). |
    |Instance Region|The region of the destination cluster. The region is the same as the destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |ECS Instance ID|Select the ID of the ECS instance on which the Kafka cluster is deployed.|
    |Database Type|Select **Kafka**.|
    |Port Number|Enter the service port number of the Kafka cluster. The default port number is 9092.|
    |Database Account|Enter the username that is used to log on to the Kafka cluster. If no authentication is enabled for the Kafka cluster, you do not need to enter the username.|
    |Database Password|Enter the password of the username. If no authentication is enabled for the Kafka cluster, you do not need to enter the password.|
    |Topic|Click **Get Topic List**, and select a topic name from the drop-down list.|
    |Kafka Version|Select the version of the destination Kafka cluster.|
    |Encryption|Select **Non-encrypted** or **SCRAM-SHA-256** based on your business and security requirements.|

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelist of the source PolarDB cluster and the inbound rule of the destination ECS instance. This ensures that DTS servers can connect to the source cluster and destination instance.

8.  Select the objects to be synchronized.

    ![Select the objects to be synchronized](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9110398951/p39868.png)

    |Setting|Description|
    |-------|-----------|
    |Select the objects to be synchronized|Select tables from the **Available** section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the tables to the **Selected** section.**Note:** DTS maps the table names to the topic name that you select in Step 6. If you want to change the topic, you can use the object name mapping feature. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md). |

9.  In the lower-right corner of the page, click **Next**.

10. Configure initial synchronization.

    ![Kafka: Configure initial synchronization](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6703019951/p87942.png)

    |Parameter|Description|
    |---------|-----------|
    |**Initial Synchronization**|Select both **Initial Schema Synchronization** and **Initial Full Data Synchronization**. DTS synchronizes the schemas and historical data of the required objects and then synchronizes incremental data.|
    |**Filter options**|**Ignore DDL in incremental synchronization phase** is selected by default. In this case, DTS does not synchronize DDL operations that are performed on the source database during incremental data synchronization.|

11. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, a precheck is performed. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
12. Close the Precheck dialog box after the following message is displayed: **The precheck is passed.** Then, the data synchronization task starts.

    You can view the status of the data synchronization task on the Synchronization Tasks page.

    ![View the status of a data synchronization task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0210398951/p39871.png)


