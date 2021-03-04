# Synchronize data from an ApsaraDB RDS for MySQL instance to a ClickHouse cluster

ClickHouse is a column-oriented database management system \(DBMS\) for online analytical processing \(OLAP\). Compared with traditional analytical databases, ClickHouse provides faster aggregate queries on large and wide tables. This topic describes how to synchronize data from an ApsaraDB RDS for MySQL instance to a ClickHouse cluster by using Data Transmission Service \(DTS\). You can also follow the procedure to configure data synchronization tasks for user-created MySQL databases. The data synchronization feature allows you to transfer and analyze data with high efficiency.

-   The tables to be synchronized from the source database contain primary keys.
-   The destination ClickHouse cluster is created. For more information, see [Create a ClickHouse cluster](https://help.aliyun.com/document_detail/145992.htm).

## Precautions

-   In this scenario, DTS cannot synchronize data definition language \(DDL\) operations.
-   DTS uses the read and write resources of the source and destination databases during initial full data synchronization. This may increase the loads of the database servers. Before you synchronize data, evaluate the impact of data synchronization on the performance of the source and destination databases. We recommend that you synchronize data during off-peak hours.
-   A maximum of 256 databases can be synchronized to the ClickHouse cluster.
-   The names of the databases, tables, and columns to be synchronized must comply with the naming conventions of ClickHouse. For more information, see [Object naming conventions](https://help.aliyun.com/document_detail/145990.htm#title-p2z-q7x-cw4).

## Initial synchronization types

|Initial synchronization type|Description|
|----------------------------|-----------|
|Initial schema synchronization|DTS synchronizes the schemas of tables from the source database to the destination database. **Warning:** MySQL and ClickHouse are heterogeneous databases. DTS does not ensure that the schemas of the source and destination databases are consistent after initial schema synchronization. We recommend that you evaluate the impact of data type conversion on your business. For more information, see [Data type mappings for initial schema synchronization](/intl.en-US/Data Synchronization/Data type mappings for initial schema synchronization.md). |
|Initial full data synchronization|DTS synchronizes the historical data of tables from the source database to the destination database. Historical data is the basis for subsequent incremental data synchronization.|
|Initial incremental data synchronization|DTS synchronizes incremental data from the source database to the destination database in real time. DTS can synchronize the following SQL operations during incremental data migration:

Data manipulation language \(DML\) operations: INSERT and UPDATE

**Warning:** The data consistency between the source and destination databases is not guaranteed because DTS cannot synchronize the DELETE operations. |

## Procedure

1.  Purchase a data synchronization instance. For more information, see [Purchase a DTS instance]().

    **Note:** On the buy page, set Source Instance to **MySQL**, set Target Instance to **ClickHouse**, and set Synchronization Topology to **One-Way Synchronization**.

2.  Configure the source and destination databases.

    ![Configure the source and destination databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9784412161/p129240.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|Select **RDS Instance**.|
    |Instance Region|The source region that you selected on the buy page. You cannot change the value of this parameter.|
    |Instance ID|Select the ID of the source RDS instance.|
    |Database Account|Enter the database account of the source RDS instance. The account must have the SELECT permission on the required objects, the REPLICATION CLIENT permission, the REPLICATION SLAVE permission, and the SHOW VIEW permission. **Note:** If the database engine of the source RDS instance is **MySQL 5.5** or **MySQL 5.6**, you do not need to configure the **database account** or **database password**. |
    |Database Password|Enter the password of the source database account.|
    |Encryption|Select **Non-encrypted** or **SSL-encrypted**. If you want to select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before you configure the data synchronization task. For more information, see [Configure SSL encryption on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96120.htm). **Note:** The **Encryption** parameter is available only for regions in mainland China and the China \(Hong Kong\) region. |
    |Destination Instance Details|Instance Type|Select **ClickHouse**.|
    |Instance Region|The destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |ClickHouse Cluster ID|Select the ID of the destination ClickHouse cluster.|
    |Database Account|Enter the database account of the destination ClickHouse cluster.|
    |Database Password|Enter the password of the destination database account.|

3.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    In this step, DTS adds the CIDR blocks of DTS servers to the whitelists of the source instance and the destination cluster. This ensures that DTS servers can connect to the source instance and the destination cluster.

4.  Select the synchronization policy and the objects to be synchronized.

    ![Select the synchronization policy and the objects to be synchronized](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9784412161/p129249.png)

    |Parameter|Description|
    |:--------|:----------|
    |Initial Synchronization|Select **Initial Schema Synchronization**, **Initial Full Data Synchronization**, and **Initial Incremental Data Synchronization**. For more information, see [Initial synchronization types](#section_apr_reo_e67).|
    |Processing Mode In Existed Target Table|    -   **Pre-check and Intercept**: checks whether the destination database contains tables that have the same names as tables in the source database. If the source and destination databases do not contain identical table names, the precheck is passed. Otherwise, an error is returned during precheck and the data synchronization task cannot be started.

**Note:** You can use the object name mapping feature to change the names of the tables that are synchronized to the destination database. You can use this feature if the source and destination databases contain identical table names and the tables in the destination database cannot be deleted or renamed. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md).

    -   **Ignore**: skips the precheck for identical table names in the source and destination databases.

**Warning:** If you select **Ignore**, data consistency is not guaranteed and your business may be exposed to potential risks.

        -   DTS does not synchronize the data records that have the same primary keys as the data records in the destination database during initial full data synchronization. This occurs if the source and destination databases have the same schema. However, DTS synchronizes these data records during incremental data synchronization.
        -   If the source and destination databases have different schemas, initial data synchronization may fail. In this case, only some columns are synchronized or the data synchronization task fails. |
    |Merge Multi Tables|    -   **Yes**: In online transaction processing \(OLTP\) scenarios, sharding is implemented to speed up the response to business tables. You can merge multiple source tables that have the same schema into a single destination table. This feature allows you to synchronize data from multiple tables in the source database to a single table in the ClickHouse cluster.

**Note:**

        -   DTS adds a column named `__dts_data_source` to the destination table in the ClickHouse cluster. This column is used to record the data source. The data type of this column is varchar. DTS specifies the column values based on the following format: `<Data synchronization instance ID>:<Source database name>. <Source table name>`. Such column values allow DTS to identify each source table. For example, `dts********:dtstestdata.customer1` indicates that the source table is customer1.
        -   If you set this parameter to Yes, all of the selected source tables in the task are merged into the destination table. If you do not need to merge specific source tables, you can create a separate data synchronization task for these tables.
    -   **No** is selected by default. |
    |Synchronization Type|Only the following DML operations are supported: INSERT and UPDATE.|
    |Select the objects to be synchronized|Select one or more objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section. You can select columns, tables, or databases as the objects to be synchronized. After an object is synchronized to the destination cluster, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are synchronized to the destination cluster. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md).

**Warning:** If you set **Merge Multi Tables** to **Yes** and select multiple source tables, you must change their names to the same table name in the ClickHouse cluster. Otherwise, data becomes inconsistent. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md). |
    |Specify the retry time for failed connection to the source or destination database|By default, if DTS fails to connect to the source or destination database, DTS retries within the next 12 hours. You can specify the retry time based on your needs. If DTS reconnects to the source and destination databases within the specified time, DTS resumes the data synchronization task. Otherwise, the data synchronization migratetask fails.**Note:** When DTS retries a connection, you are charged for the DTS instance. We recommend that you specify the retry time based on your business needs. You can also release the DTS instance at your earliest opportunity after the source and destination instances are released. |

5.  Click **Next**.

6.  Specify the primary key column, sort key, partition key, and sharding key of the tables that you want to synchronize to the ClickHouse cluster.

    ![Advanced settings for ClickHouse](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9784412161/p129259.png)

    **Note:**

    -   Primary key columns and sort keys can be composite keys. In other words, you can select multiple fields as the primary key columns and sort keys from the drop-down lists. However, you must enter a field or a field expression for the partition key and sharding key. For more information about the primary key columns, sort keys, partition keys, and sharding keys, see [Create Table](https://help.aliyun.com/document_detail/146116.html).
    -   You can filter tables by table name and set partition keys and sharding keys for a large number of tables at a time. For example, you can enter a field or a field expression in the text box next to **Set All Partition Keys To**, and then click **OK**.
7.  Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    You can view the status of the data synchronization task on the Synchronization Tasks page.

    ![View the status of a data synchronization task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5130359951/p41059.png)


