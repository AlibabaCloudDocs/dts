# Synchronize data from a user-created MySQL database to Message Queue for Apache Kafka

This topic describes how to synchronize data from a user-created MySQL database to Message Queue for Apache Kafka by using Data Transmission Service \(DTS\). The data synchronization feature allows you to extend message processing capabilities.

-   A MySQL database of version 5.1, 5.5, 5.6, 5.7, or 8.0 is created.
-   A Kafka instance of version 0.10.1.0 to 1.0.2 is created.
-   In the destination Kafka instance, a topic is created to receive the synchronized data. For more information, see [Create a topic](https://www.alibabacloud.com/help/zh/doc-detail/99952.html#title-7lc-gsy-p0n).

[Message Queue for Apache Kafka](https://www.alibabacloud.com/help/zh/doc-detail/68151.htm) is a distributed, high-throughput, and scalable message queue service that is provided by Alibaba Cloud. It provides fully managed services for the open source Apache Kafka to resolve the long-standing shortcomings of open source products. Message Queue for Apache Kafka allows you to focus on business development without spending much time in deployment and O&M. Message Queue for Apache Kafka is used in big data scenarios such as log collection, monitoring data aggregation, streaming data processing, and online and offline analysis. It is important for the big data ecosystem.

## Precautions

-   DTS uses read and write resources of the source and destination databases during initial full data synchronization. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you synchronize data, evaluate the impact of data synchronization on the performance of the source and destination databases. We recommend that you synchronize data during off-peak hours. For example, you can synchronize data when the CPU utilization of the source and destination databases is less than 30%.
-   The source database must have PRIMARY KEY or UNIQUE constraints and all fields must be unique. Otherwise, the destination database may contain duplicate data records.

## Limits

-   You can select only tables as the objects to be synchronized.
-   DTS does not synchronize the data in a renamed table to the destination Kafka cluster. This applies if the new table name is not included in the objects to be synchronized. To synchronize the data in a renamed table to the destination Kafka cluster, you must **reselect the objects to be synchronized**. For more information, see [t17133.md\#](/intl.en-US/Data Synchronization/Synchronization task management/Add an object to a data synchronization task.md).

## Data format

The data that is synchronized to the Kafka cluster is stored in the Avro format. For more information, see [DTS Avro schema](https://github.com/LioRoger/subscribe_example/tree/master/avro).

**Note:** After the data is synchronized to the Kafka cluster, you must parse the data based on the Avro schema.

## Before you begin

[t961261.md\#]()

## Procedure

1.  Purchase a data synchronization instance. For more information, see [t17076.md\#]().

    **Note:** On the buy page, set Source Instance to **MySQL**, set Target Instance to **Kafka**, and set Synchronization Topology to **One-Way Synchronization**.

2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

3.  In the left-side navigation pane, click **Data Synchronization**.

4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column.

6.  Configure the source and destination instances.

    ![Configure the source and destination instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2110398951/p75455.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|Select an instance type based on the deployment of the source database. In this example, select **User-Created Database in ECS Instance**. You can also follow the procedure to configure data synchronization tasks for other types of user-created MySQL databases.|
    |Instance Region|The source region that you selected on the buy page. You cannot change the value of this parameter.|
    |ECS Instance ID|Select the ID of the ECS instance that hosts the user-created MySQL database.|
    |Database Type|The value of this parameter is set to **MySQL** and cannot be changed.|
    |Port Number|Enter the service port number of the user-created MySQL database.|
    |Database Account|Enter the account of the user-created MySQL database. The account must have the SELECT permission on the required objects, the REPLICATION CLIENT permission, the REPLICATION SLAVE permission, and the SHOW VIEW permission.|
    |Database Password|Enter the password of the source database account.|
    |Destination Instance Details|Instance Type|Select **User-Created Database Connected over Express Connect, VPN Gateway, or Smart Access Gateway**. **Note:** You cannot select Message Queue for Apache Kafka as the instance type. You can use Message Queue for Apache Kafka as a user-created Kafka database to configure data synchronization. |
    |Instance Region|The destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |Peer VPC|Select the ID of the VPC to which the destination Message Queue for Apache Kafka instance belongs. To obtain the VPC ID, you can log on to the Message Queue for Apache Kafka console and go to the Instance Details page of the Message Queue for Apache Kafka instance. In the **Basic Information** section, you can view the VPC ID.![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2110398951/p74095.png) |
    |Database Type|Select **Kafka**.|
    |IP Address|Enter an IP address from the **Default Endpoint** parameter of the Message Queue for Apache Kafka instance. **Note:** To obtain an IP address, you can log on to the Message Queue for Apache Kafka console and go to the Instance Details page of the Message Queue for Apache Kafka instance. In the **Basic Information** section, you can obtain an IP address from the **Default Endpoint** parameter. |
    |Port Number|Enter the service port number of the Message Queue for Apache Kafka instance. The default port number is 9092.|
    |Database Account|Enter the username that is used to log on to the Message Queue for Apache Kafka instance. **Note:** If the instance type of the Message Queue for Apache Kafka instance is **VPC Instance**, you do not need to specify the **database account** or **database password**. |
    |Database Password|Enter the password of the username.|
    |Topic|Click **Get Topic List**, and select a topic name from the drop-down list.|
    |Kafka Version|Select the version of the Message Queue for Apache Kafka instance.|
    |Encryption|Select **Non-encrypted** or **SCRAM-SHA-256** based on your business and security requirements.|

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

8.  Select the objects to be synchronized.

    ![Select the objects to be synchronized](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9110398951/p39868.png)

    |Setting|Description|
    |-------|-----------|
    |Select the objects to be synchronized|Select tables from the **Available** section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the tables to the **Selected** section.**Note:** DTS maps the table names to the topic name that you select in Step 6. If you want to change the topic, you can use the object name mapping feature. For more information, see [t947854.md\#](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md). |

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


