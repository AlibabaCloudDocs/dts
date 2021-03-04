# Migrate data from an ApsaraDB RDS for MySQL instance to a user-created Kafka cluster

Kafka is a distributed message queue service that features high throughput and high scalability. Kafka is widely used for big data analytics such as log collection, data aggregation, streaming processing, and online and offline analysis. It is important for the big data ecosystem. This topic describes how to migrate data from an ApsaraDB RDS for MySQL instance to a user-created Kafka cluster by using Data Transmission Service \(DTS\). The user-created Kafka cluster has a public IP address. The data migration feature allows you to extend message processing capabilities.

-   A Kafka cluster is created and the Kafka version is 0.10.1.0 to 1.0.2.
-   The service port of the user-created Kafka cluster is accessible over the Internet.

If you use the data synchronization feature, the deployment of the user-created Kafka cluster must belong to one of the following types:

-   **User-created database that is hosted on ECS**
-   **User-created database that is connected over Express Connect, VPN Gateway, or Smart Access Gateway**
-   **Database that has no public IP address or port number \(connected over Database Gateway\)**
-   **User-created database that is connected over Cloud Enterprise Network \(CEN\)**

If your on-premises Kafka cluster does not belong to one of the preceding types, you can open the service port of the Kafka cluster to the Internet. Then, you can use the data migration feature to synchronize data to the Kafka cluster.

## Precautions

-   DTS uses read and write resources of the source and destination databases during full data migration. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you migrate data, evaluate the impact of data migration on the performance of the source and destination databases. We recommend that you migrate data during off-peak hours. For example, you can migrate data when the CPU utilization of the source and destination databases is less than 30%.
-   The source database must have PRIMARY KEY or UNIQUE constraints and all fields must be unique. Otherwise, the destination database may contain duplicate data records.
-   You can select only tables as the objects to be migrated.

## Data format

The data that is migrated to the Kafka cluster is stored in the Avro format. You must parse the migrated data based on the Avro schema. For more information, see [DTS Avro schema](https://github.com/LioRoger/subscribe_example/tree/master/avro).

## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|--------------|--------------------------|--------------------|
|Schema migration and full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|
|Incremental data migration|Charged. For more information, see [Pricing]().|

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Data Migration**.

3.  At the top of the Migration Tasks page, select the region where the destination cluster resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.

5.  Configure the source and destination databases.

    ![Configure the source and destination databases](../images/p146904.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|Instance Type|Select **RDS Instance**.|
    |Instance Region|Select the region where the source RDS instance resides.|
    |Instance ID|Select the ID of the source RDS instance.|
    |Database Account|Enter the database account of the source RDS instance. The account must have the SELECT permission on the objects to be migrated, the REPLICATION CLIENT permission, the REPLICATION SLAVE permission, and the SHOW VIEW permission.|
    |Database Password|Enter the password of the source database account.|
    |Encryption|Select **Non-encrypted** or **SSL-encrypted**. If you select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before you configure the data migration task. For more information, see [Configure SSL encryption for an ApsaraDB RDS for MySQL instance](~~96120~~). **Note:** The **Encryption** parameter is available only for regions in mainland China and the China \(Hong Kong\) region. |
    |Destination Database|Instance Type|Select **User-Created Database with Public IP Address**.|
    |Instance Region|You do not need to specify this parameter.|
    |Database Type|Select **Kafka**.|
    |Hostname or IP Address|Enter the endpoint that is used to connect to the user-created Kafka cluster. In this example, enter the public IP address.|
    |Port Number|Enter the service port number of the Kafka cluster. The default port number is 9092.|
    |Database Account|Enter the username that is used to log on to the Kafka cluster. If no authentication is enabled for the Kafka cluster, you do not need to enter the username.|
    |Database Password|Enter the password of the username. If no authentication is enabled for the Kafka cluster, you do not need to enter the password.|
    |Topic|Click **Get Topic List**, and select a topic name from the drop-down list.|
    |Kafka Version|Select the version of the destination Kafka cluster.|
    |Encryption|Select **Non-encrypted** or **SCRAM-SHA-256** based on your business and security requirements.|

6.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelist of the source ApsaraDB RDS for MySQL instance. This ensures that DTS servers can connect to the source instance.

7.  Select the migration types, the migration policy, and the objects to be migrated.

    ![Select the migration types, the migration policy, and the objects to be migrated](../images/p146905.png)

    |Parameter|Description|
    |:--------|:----------|
    |Migration Types|Select **Schema Migration**, **Full Data Migration**, and **Incremental Data Migration**.**Note:** If **Incremental Data Migration** is not selected, do not write data to the source database during full data migration. This ensures data consistency between the source and destination databases. |
    |Policy for Migrating Data to Kafka Partitions|Select a migration policy based on your business requirements. For more information, see [Specify the policy for synchronizing data to Kafka partitions]().|
    |Objects|Select one or more tables from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the tables to the Selected section.**Note:** DTS automatically maps the table names to the topic name that you selected in Step 5. For information about how to change the topic name, see [Object name mapping](/intl.en-US/Data Migration/Migration task management/Object name mapping.md). |

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


