# Synchronize data from a PolarDB for MySQL cluster to an Elasticsearch cluster

Alibaba Cloud Elasticsearch is compatible with open source Elasticsearch features such as Security, Machine Learning, Graph, and Application Performance Management \(APM\). Alibaba Cloud Elasticsearch provides capabilities such as enterprise-level access control, security monitoring and alerts, and automatic report generation. You can use Alibaba Cloud Elasticsearch to search and analyze data. This topic describes how to synchronize data from a PolarDB for MySQL cluster to an Elasticsearch cluster by using Data Transmission Service \(DTS\).

-   An Elasticsearch cluster of version 5.5, 5.6, 6.3, 6.7, or 7.4 is created. For more information, see [Create an Elasticsearch cluster](https://www.alibabacloud.com/help/zh/doc-detail/69055.htm).
-   The binary logging feature is enabled for the PolarDB for MySQL cluster. For more information, see [Enable binary logging](https://www.alibabacloud.com/help/zh/doc-detail/113546.htm)

## Precautions

-   DTS uses read and write resources of the source and destination databases during initial full data synchronization. This may increase the database load. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before synchronizing data, you must evaluate the performance of the source and destination databases. We recommend that you synchronize data during off-peak hours. For example, you can synchronize data when the CPU usage of the source and destination databases is less than 30%.
-   DTS does not synchronize data definition language \(DDL\) operations. If a DDL operation is performed on a table in the source database during data synchronization, you must perform the following steps: Remove the table from the required objects, remove the index for the table from the Elasticsearch cluster, and then add the table to the required objects. For more information, see [Remove an object from a data synchronization task](/intl.en-US/Data Synchronization/Synchronization task management/Remove an object from a data synchronization task.md) and [Add an object to a data synchronization task](/intl.en-US/Data Synchronization/Synchronization task management/Add an object to a data synchronization task.md).
-   To add columns to the table that you want to synchronize, perform the following steps: Modify the mapping of the table in the Elasticsearch cluster, perform data definition language \(DDL\) operations in the PolarDB for MySQL cluster, and then pause and start the data synchronization task.

## SQL operations that can be synchronized

INSERT, DELETE, and UPDATE

## Data type conversion

The data types of the PolarDB for MySQL cluster and the Elasticsearch cluster do not have one-to-one correspondence. During initial schema synchronization, DTS converts the data types of the PolarDB for MySQL cluster into the data types of the Elasticsearch cluster. For more information, see [Data type mappings for initial schema synchronization](/intl.en-US/Data Synchronization/Data type mappings for initial schema synchronization.md).

## Procedure

1.  Purchase a data synchronization instance. For more information, see [Purchase procedure]().

    **Note:** On the buy page, set Source Instance to **PolarDB**, set Target Instance to **Elasticsearch**, and set Synchronization Topology to **One-Way Synchronization**.

2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

3.  In the left-side navigation pane, click **Data Synchronization**.

4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column.

6.  Configure the source and destination instances.

    ![Configure the source and destination instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8310398951/p85904.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|The value of this parameter is set to **PolarDB Instance** and cannot be changed.|
    |Instance Region|The region of the source cluster. The region is the same as the source region that you selected on the buy page. You cannot change the value of this parameter.|
    |PolarDB Instance ID|Select the ID of the source PolarDB for MySQL cluster.|
    |Database Account|Enter the database account of the PolarDB for MySQL cluster. **Note:** The account must have the read permission on the source database. |
    |Database Password|Enter the password of the source database account.|
    |Destination Instance Details|Instance Type|The value of this parameter is set to **Elasticsearch** and cannot be changed.|
    |Instance Region|The region of the destination cluster. The region is the same as the destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |Elasticsearch|Select the ID of the destination Elasticsearch cluster.|
    |Database Account|Enter the account that is used to connect to the Elasticsearch cluster. The default account is elastic.|
    |Database Password|Enter the password of the destination database account.|

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelists of the PolarDB for MySQL cluster and the Elasticsearch cluster. This ensures that DTS servers can connect to the source and destination clusters.

8.  Configure the index name, the processing mode of identical index names, and the objects to be synchronized.

    ![Select the objects to be synchronized](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8310398951/p58418.png)

    |Parameter|Description|
    |:--------|:----------|
    |Index Name|    -   **Table Name**

If you select **Table Name**, the name of the index that is created in the Elasticsearch cluster is the same as the name of the table. In this example, the index name is customer.

    -   **DatabaseName\_TableName**

If you select **DatabaseName\_TableName**, the name of the index that is created in the Elasticsearch cluster is <Database name\>\_<Table name\>. In this example, the index name is dtstestdata\_customer. |
    |Processing Mode In Existed Target Table|    -   **Pre-check and Intercept**: checks whether the destination database contains indexes that have the same names as tables in the source database. If the destination database does not contain indexes that have the same names as tables in the source database, the precheck is passed. Otherwise, an error is returned during precheck and the data synchronization task cannot be started.

**Note:** If indexes in the destination database have the same names as tables in the source database, and cannot be deleted or renamed, you can use the object name mapping feature. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md).

    -   **Ignore**: skips the precheck for identical index names in the source and destination databases.

**Warning:** If you select **Ignore**, data consistency is not guaranteed and your business may be exposed to potential risks.

        -   If the source and destination databases have the same mappings and the primary key of a record in the destination database is the same as that in the source database, the record remains unchanged during initial data synchronization. However, the record is overwritten during incremental data synchronization.
        -   If the source and destination databases have different mappings, initial data synchronization may fail. In this case, only some columns are synchronized or the data synchronization task fails. |
    |Objects to be synchronized|Select objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section.

You can select databases and tables as the objects to be synchronized. |

9.  In the Selected section, move the pointer over a table, and then click **Edit**. In the Edit Table dialog box, configure parameters for the table in the Elasticsearch cluster, such as the index name and type name.

    ![Configure parameters such as the index name](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3251883061/p58425.png)

    |Parameter|Description|
    |---------|-----------|
    |Index Name|For more information, see [Terms](https://www.alibabacloud.com/help/zh/doc-detail/58107.htm) in the Elasticsearch documentation. **Warning:**

    -   The only type of special characters that an index name and type name can contain is underscore \(\_\).
    -   To synchronize multiple source tables with the same schema to a destination object, you must repeat this step to set the same index name and type name for the tables. |
    |Type Name|
    |Filter|Specify SQL conditions to filter data. Only data that meets the specified conditions is synchronized to the destination instance. For more information, see [Use SQL conditions to filter data](/intl.en-US/Data Synchronization/Synchronization task management/Use SQL conditions to filter data.md).|
    |IsPartition|Select whether to set partitions. If you select **Yes**, you must also specify the **partition key column** and **number of partitions**.|
    |\_id value|    -   **Primary key column**

Composite primary key fields are merged into one column.

    -   **Business key**

If you select a **business key**, you must also specify the **business key column**. |
    |add param|Select the **column parameter** and **parameter value**. For more information, see [Mapping parameters](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-params.html) in the Elasticsearch documentation.|

10. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, a precheck is performed. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
11. Close the Precheck dialog box after the following message is displayed: **The precheck is passed**.

12. Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    On the Synchronization Tasks page, view the status of the data synchronization task.

    ![View the status of a data synchronization task.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5130359951/p41059.png)


## Check the index and data

If the data synchronization task is in the **Synchronizing** state, you can connect to the Elasticsearch cluster by using the Elasticsearch-Head plug-in. Then, you can check whether the index is created and data is synchronized as expected. For more information, see [Use Cerebro to access an Elasticsearch cluster](https://www.alibabacloud.com/help/zh/doc-detail/120755.htm).

**Note:** If the index is not created or data is not synchronized as expected, you can delete the index and data, and then configure the data synchronization task again.

![View data in Elasticsearch](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8310398951/p58790.png)

