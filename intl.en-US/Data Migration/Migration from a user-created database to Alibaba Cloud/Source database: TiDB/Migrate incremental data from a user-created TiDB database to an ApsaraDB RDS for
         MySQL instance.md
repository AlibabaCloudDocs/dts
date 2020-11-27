# Migrate incremental data from a user-created TiDB database to an ApsaraDB RDS for MySQL instance

This topic describes how to migrate incremental data from a user-created TiDB database to an ApsaraDB RDS for MySQL instance by using Data Transmission Service \(DTS\). Incremental data migration allows you to ensure service continuity when you migrate data to Alibaba Cloud. In this example, Pump, Drainer, and a Kafka cluster are deployed.

[Create an ApsaraDB RDS for MySQL instance](/intl.en-US/RDS MySQL Database/Quick start/Create an ApsaraDB RDS for MySQL instance.md)

**Note:**

-   The destination ApsaraDB RDS for MySQL instance must reside in the China \(Hangzhou\), China \(Shanghai\), China \(Qingdao\), China \(Beijing\), China \(Shenzhen\), China \(Zhangjiakou-Beijing Winter Olympics\), China \(Hong Kong\), Singapore \(Singapore\), US \(Silicon Valley\), or US \(Virginia\) region.
-   The available storage space of the destination ApsaraDB RDS for MySQL instance must be larger than the total size of the data in the user-created TiDB database.

![Migrate incremental data from TiDB](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4034948951/p113300.png)

The binary log format and implementation mechanism of a TiDB database are different from those of a MySQL database. To migrate incremental data and minimize modifications to the source TiDB database, you must deploy Pump, Drainer, and a Kafka cluster.

Pump records the binary log files that are generated in TiDB in real time, and sends the binary log files to Drainer. Drainer writes the binary log files to the downstream Kafka cluster. During incremental data migration, DTS retrieves data from the Kafka cluster and migrates the data to the destination database in real time. For example, DTS can migrate incremental data to an ApsaraDB RDS for MySQL instance.

## Precautions

-   DTS uses read and write resources of the source and destination databases during full data migration. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you migrate data, evaluate the impact of data migration on the performance of the source and destination databases. We recommend that you migrate data during off-peak hours. For example, you can migrate data when the CPU utilization of the source and destination databases is less than 30%.
-   The tables to be migrated in the source database must have PRIMARY KEY or UNIQUE constraints and all fields must be unique. Otherwise, the destination database may contain duplicate data records.
-   DTS uses the `ROUND(COLUMN,PRECISION)` function to retrieve values from columns of the FLOAT or DOUBLE data type. If you do not specify a precision, DTS sets the precision for the FLOAT data type to 38 digits and the precision for the DOUBLE data type to 308 digits. You must check whether the precision settings meet your business requirements.
-   DTS automatically creates a destination database in the ApsaraDB RDS for MySQL instance. However, if the name of the source database is invalid, you must manually create a database in the ApsaraDB RDS for MySQL instance before you configure the data migration task.

    **Note:** For more information about how to create a database and the database naming conventions, see [Create a database on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96105.htm).

-   If a data migration task fails, DTS automatically resumes the task. Before you switch your workloads to the destination instance, stop or release the data migration task. Otherwise, the data in the source database will overwrite the data in the destination instance after the task is resumed.

## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|--------------|--------------------------|--------------------|
|Schema migration and full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|
|Incremental data migration|Charged. For more information, see [Pricing]().|

## Migration types

|Migration type|Description|
|--------------|-----------|
|Schema migration|DTS migrates the schemas of the required objects to the destination database. DTS supports schema migration for views, tables, and databases. **Warning:** TiDB and MySQL are heterogeneous databases. DTS does not ensure that the schemas of the source and destination databases are consistent after schema migration. We recommend that you evaluate the impact of data type conversion on your business. For more information, see [Data type mappings between heterogeneous databases](/intl.en-US/Data Migration/Data type mappings between heterogeneous databases.md). |
|Full data migration|DTS migrates historical data of the required objects to the destination database. **Note:** During full data migration, concurrent INSERT operations cause fragmentation in the tables of the destination database. After full data migration is complete, the tablespace of the destination database is larger than that of the source database. |
|Incremental data migration|DTS retrieves binary log files that are generated in TiDB from the Kafka cluster, and migrates incremental data to the destination database in real time. During incremental data migration, the following SQL operations can be synchronized: -   Data manipulation language \(DML\) operations: INSERT, UPDATE, and DELETE
-   Data definition language \(DDL\) operations: CREATE TABLE, DROP TABLE, ALTER TABLE, RENAME TABLE, TRUNCATE TABLE, CREATE VIEW, DROP VIEW, and ALTER VIEW

Incremental data migration allows you to ensure service continuity when you migrate data from a user-created TiDB database to Alibaba Cloud. |

## Before you begin

**Note:** The server on which the source database is deployed must be in the same internal network as the servers on which Pump, Drainer, and the Kafka cluster are deployed. This minimizes the impact of network latency on incremental data migration.

1.  Deploy Pump and Drainer. For more information, see [TiDB Binlog Cluster Deployment](https://pingcap.com/docs-cn/stable/tidb-binlog/deploy-tidb-binlog/).

2.  Modify the configuration file of Drainer and specify a Kafka cluster to receive data from Drainer. For more information, see [Binlog Slave Client User Guide](https://pingcap.com/docs-cn/stable/tidb-binlog/binlog-slave-client/).

3.  Deploy a Kafka cluster by using one of the following methods:

    -   Deploy a user-created Kafka cluster. For more information, visit [Apache Kafka official website](https://kafka.apache.org/).

        **Warning:** We recommend that you set the `message.max.bytes` and `replica.fetch.max.bytes` parameters for the Kafka broker to greater values. We also recommend that you set the `fetch.message.max.bytes` parameter for the Kafka consumer to a greater value. These settings ensure that the Kafka cluster can receive the binary log files that are generated in TiDB. For more information, see [Kafka 2.5 Documentation](https://kafka.apache.org/documentation/#configuration).

    -   Purchase and deploy a [Message Queue for Apache Kafka](/intl.en-US/Introduction/What is Message Queue for Apache Kafka?.md) instance. For more information, see [Quick start of Message Queue for Apache Kafka](/intl.en-US/Quick-start/Overview.md).

        **Note:** The Message Queue for Apache Kafka instance must be deployed in the same virtual private cloud \(VPC\) as the source database server. This ensures reliable data transmission and minimizes the impact of network latency on incremental data migration.

4.  Create a topic in the user-created Kafka cluster or the Message Queue for Apache Kafka instance.

5.  Add the CIDR blocks of DTS servers to the whitelist of the TiDB database. For more information, see [Add the CIDR blocks of DTS servers to the security settings of on-premises databases]().


## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Data Migration**.

3.  At the top of the Migration Tasks page, select the region where the destination cluster resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.

5.  Configure the source and destination databases for the data migration task.

    1.  Configure the task name and source database.

        ![Configure the task name and source database](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4034948951/p113247.png)

        |Parameter|Description|
        |:--------|:----------|
        |Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
        |Instance Type|Select an instance type based on the deployment of the source database. In this example, select **User-Created Database in ECS Instance**. **Note:** If you select other instance types, you must prepare the environment that is required for the source database. For more information, see [Preparation overview](). |
        |Instance Region|Select the region of the ECS instance on which the source TiDB database is deployed.|
        |Database Type|Select **TiDB**.|
        |Port Number|Enter the service port number of the source TiDB database. The default port number is **4000**.|
        |Database Account|Enter the account of the source TiDB database. The account must have the SELECT permission on the objects to be migrated and the SHOW VIEW permission.|
        |Database Password|Enter the password of the source database account. **Note:** After you specify the source database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
        |Incremental migration or not|Specify whether to perform incremental data migration. In this example, select **Yes**. For information about how to perform only full data migration, see [Migrate full data from a user-created TiDB database to an ApsaraDB RDS for MySQL instance](/intl.en-US/Data Migration/Migration from a user-created database to Alibaba Cloud/Source database: TiDB/Migrate full data from a user-created TiDB database to an ApsaraDB RDS for MySQL instance.md).|
        |Kafka Cluster Type|Select an instance type based on the deployment of the Kafka cluster. In this example, select **User-Created Database in ECS Instance**. If you select other instance types, you must prepare the environment that is required for the database. For more information, see [Preparation overview](). **Note:** You cannot select Message Queue for Apache Kafka as the instance type. If you deploy a Message Queue for Apache Kafka instance, you must select **User-Created Database Connected over Express Connect, VPN Gateway, or Smart Access Gateway**. Then, you must select the VPC to which the Message Queue for Apache Kafka instance belongs. |
        |Instance Region|The value of this parameter is the same as the region of the source database and cannot be changed.|
        |ECS Instance ID|Select the ID of the ECS instance that hosts the Kafka cluster.|
        |Kafka Port Number|Enter the service port number of the Kafka cluster. The default port number is 9092.|
        |Kafka Cluster Account|Enter the username that is used to log on to the Kafka cluster. If no authentication is enabled for the Kafka cluster, you do not need to enter the username.|
        |Kafka Cluster Password|Enter the password of the cluster account. If no authentication is enabled for the Kafka cluster, you do not need to enter the password.|
        |Topic|Click **Get Topic List**, and select a topic name from the drop-down list.|
        |Kafka Version|Select the version of the user-created Kafka cluster.|
        |Kafka Cluster Encryption|Select **Non-encrypted** or **SCRAM-SHA-256** based on your business and security requirements.|

    2.  Configure the destination database.

        ![Configure the destination database](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4034948951/p113250.png)

        |Parameter|Description|
        |:--------|:----------|
        |Instance Type|Select **RDS Instance**.|
        |Instance Region|Select the region where the destination RDS instance resides.|
        |Database Account|Enter the database account of the destination RDS instance. The account must have the read and write permissions on the destination database. For more information about how to create and authorize a database account, see [Create an account on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96089.htm) and [Modify the permissions of a standard account for an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96101.htm).|
        |Database Password|Enter the password of the destination database account. **Note:** After you specify the source database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
        |Encryption|Select **Non-encrypted** or **SSL-encrypted**. If you select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before you configure the data migration task. For more information, see [Configure SSL encryption on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96120.htm). **Note:** The **Encryption** parameter is available only for regions in mainland China and the China \(Hong Kong\) region. |

6.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    DTS adds the CIDR blocks of DTS servers to the whitelist of the destination ApsaraDB RDS for MySQL instance. This ensures that DTS servers can connect to the destination RDS instance.

7.  Select the migration types and the objects to be migrated.

    ![Select the migration types and the objects to be migrated](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4034948951/p113245.png)

    |Setting|Description|
    |:------|:----------|
    |Select the migration types|    -   To perform only full data migration, select **Schema Migration** and **Full Data Migration**.
    -   To ensure service continuity during data migration, select **Schema Migration**, **Full Data Migration**, and **Incremental Data Migration**. In this example, all of the three migration types are selected. |
    |Select the objects to be migrated|Select objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section. **Note:**

    -   You can select columns, tables, or databases as the objects to be migrated. If you select tables or columns as the objects to be migrated, DTS does not migrate other objects such as views, triggers, and stored procedures to the destination database.
    -   After an object is migrated to the destination database, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are migrated to the destination database. For more information, see [Object name mapping](/intl.en-US/Data Migration/Migration task management/Object name mapping.md).
    -   If you use the object name mapping feature on an object, other objects that are dependent on the object may fail to be migrated. |

8.  In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data migration task, a precheck is performed. A data migration task can be started only if it passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and perform a precheck again.
9.  After the task passes the precheck, click **Next**.

10. In the Confirm Settings dialog box, specify the **Channel Specification** parameter and select **Data Transmission Service \(Pay-As-You-Go\) Service Terms**.

11. Click **Buy and Start** to start the data migration task.

    -   Schema migration and full data migration

        We recommend that you do not manually stop a data migration task. Otherwise, data migrated to the destination database will be incomplete. Wait until the data migration task automatically stops.

    -   Schema migration, full data migration, and incremental data migration

        An incremental data migration task does not automatically stop. You must manually stop the migration task.

        **Note:** Select an appropriate time to manually stop the migration task. For example, you can stop the migration task during off-peak hours or before you switch your workloads to the destination instance.

        1.  Wait until **Incremental Data Migration** and **The migration task is not delayed** appear in the progress bar of the migration task. Then, stop writing data to the source database for a few minutes. The delay time of **incremental data migration** may be displayed in the progress bar.
        2.  After the status of **incremental data migration** changes to **The migration task is not delayed**, manually stop the migration task.

            ![Stop an incremental data migration task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2940359951/p47604.png)


