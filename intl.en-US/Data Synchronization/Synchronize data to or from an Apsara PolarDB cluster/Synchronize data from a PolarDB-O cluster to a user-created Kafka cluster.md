# Synchronize data from a PolarDB-O cluster to a user-created Kafka cluster

Kafka is a distributed message queue service that features high throughput and high scalability. Kafka is widely used for big data analytics such as log collection, data aggregation, streaming processing, and online and offline analysis. It is important for the big data ecosystem. This topic describes how to synchronize data from a PolarDB-O cluster to a user-created Kafka cluster by using Data Transmission Service \(DTS\). The data synchronization feature allows you to extend message processing capabilities.

-   The source PolarDB-O cluster uses the latest version. For more information, see [Upgrade the minor version]().
-   The tables to be synchronized contain primary keys or UNIQUE NOT NULL indexes.
-   The value of the wal\_level parameter is set to logical for the source PolarDB-O cluster. This setting ensures that logical decoding is supported in write-ahead logging \(WAL\). For more information, see [Change the values of cluster parameters]().

## Precautions

-   In this scenario, DTS supports only<dfn class="aliterm" aliterm-def="DTS synchronizes incremental data that is generated in the source database to the destination database." \>incremental data synchronization</dfn\>.DTS does not support and
-   A single data synchronization task can synchronize data from only one database. To synchronize data from multiple databases, you must create a data synchronization task for each database.
-   To ensure that the delay time of data synchronization is accurate, DTS adds a heartbeat table named `dts_postgres_heartbeat` to the source database. The following figure shows the schema of the heartbeat table.

    ![Schema of a heartbeat table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2310398951/p94992.png)


## Procedure

1.  Purchase a data synchronization instance. For more information, see [Purchase a DTS instance]().

    **Note:** On the buy page, set Source Instance to **PolarDB**, set Target Instance to **Kafka**, and set Synchronization Topology to **One-Way Synchronization**.

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
    |Instance Region|The region of the source cluster. The region is the same as the source region that you selected on the buy page. You cannot change the value of this parameter.|
    |PolarDB Instance ID|Select the ID of the source PolarDB-O cluster.|
    |Database Name|Enter the name of the source database.|
    |Database Account|Enter the privileged account of the source PolarDB-O cluster. For more information about how to create a privileged database account, see [Create database accounts]().|
    |Database Password|Enter the password of the source database account.|
    |Destination Instance Details|Instance Type|Select an instance type based on the deployment of the user-created Kafka cluster. In this example, select **User-Created Database with Public IP Addres**. **Note:** If you select other instance types, you must prepare the environment that is required for the database. For more information, see [Preparation overview](). |
    |Instance Region|The region of the destination cluster. The region is the same as the destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |ECS Instance ID|Select the ID of the ECS instance on which the Kafka cluster is deployed.|
    |Database Type|Select **Kafka**.|
    |Port Number|Enter the service port number of the Kafka cluster. The default port number is 9092.|
    |Database Account|Enter the username that is used to log on to the Kafka cluster. If no authentication is enabled for the Kafka cluster, you do not need to enter the username.|
    |Database Password|Enter the password of the cluster account. If no authentication is enabled for the Kafka cluster, you do not need to enter the password.|
    |Topic|Click **Get Topic List**, and select a topic name from the drop-down list.|
    |Kafka Version|Select the version of the user-created Kafka cluster.|
    |Encryption|Select **Non-encrypted** or **SCRAM-SHA-256** based on your business and security requirements.|

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelist of the PolarDB-O cluster and the inbound rule of the ECS instance. This ensures that DTS servers can connect to the source and destination clusters.

8.  Select the objects to be synchronized.

    ![Select the objects to be synchronized](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2310398951/p101776.png)

    |Setting|Description|
    |:------|:----------|
    |Select the objects to be synchronized|Select one or more tables from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the tables to the Selected section. You can select only tables as the objects to be synchronized. **Note:** DTS maps the table names to the topic name that you select in [Step 6](#step_swc_fdy_awx). If you want to change the topic name, you can move the pointer over the table and click **Edit**. You must specify a topic that exists in the Kafka cluster. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md). |

9.  In the lower-right corner of the page, click **Next**.

10. Configure initial synchronization.

    ![Advanced settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2310398951/p101781.png)

    |Parameter|Description|
    |---------|-----------|
    |Initial Synchronization|**Initial Schema Synchronization** is selected by default. DTS synchronizes incremental data that is generated in the source database to the destination database.|
    |Filter options|**Ignore DDL in incremental synchronization phase** is selected by default. DTS does not synchronize data definition language \(DDL\) operations that are performed on the source database during incremental data synchronization.**Note:** The setting of this parameter does not take effect. DTS does not synchronize DDL operations that are performed on the source database regardless of whether you select this option. |

11. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, a precheck is performed. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
12. Close the Precheck dialog box after the following message is displayed: **The precheck is passed**.

13. Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    On the Synchronization Tasks page, view the status of the data synchronization task.

    ![View the status of a data synchronization task.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5130359951/p41059.png)


