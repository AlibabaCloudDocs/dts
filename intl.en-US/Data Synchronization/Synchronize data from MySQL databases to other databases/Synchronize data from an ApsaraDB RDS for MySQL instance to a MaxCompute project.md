# Synchronize data from an ApsaraDB RDS for MySQL instance to a MaxCompute project

MaxCompute \(formerly known as ODPS\) is a fast and fully managed computing platform for large-scale data warehousing. MaxCompute can process exabytes of data. This topic describes how to synchronize data from an ApsaraDB RDS for MySQL instance to a MaxCompute project by using Data Transmission Service \(DTS\).

The following operations are performed:

-   [Activate MaxCompute](https://www.alibabacloud.com/help/zh/doc-detail/58226.htm)
-   [Create a project](https://www.alibabacloud.com/help/zh/doc-detail/27815.htm)

## Precautions

-   DTS uses read and write resources of the source and destination databases during initial full data synchronization. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you synchronize data, evaluate the impact of data synchronization on the performance of the source and destination databases. We recommend that you synchronize data during off-peak hours. For example, you can synchronize data when the CPU utilization of the source and destination databases is less than 30%.
-   You can select only tables as the objects to be synchronized.
-   We recommend that you do not use gh-ost or pt-online-schema-change to perform data definition language \(DDL\) operations on objects during data synchronization. Otherwise, data synchronization may fail.
-   MaxCompute does not support the PRIMARY KEY constraint. If network errors occur, DTS may synchronize duplicate data records to MaxCompute.

## Supported source database types

You can use DTS to synchronize data from the following types of MySQL databases:

-   User-created database that is hosted on ECS
-   User-created database that is connected over Express Connect, VPN Gateway, or Smart Access Gateway
-   User-created database that is connected over Database Gateway
-   ApsaraDB RDS for MySQL instance that is owned by the same Alibaba Cloud account as the MaxCompute project or a different Alibaba Cloud account from the MaxCompute project

This topic uses an **ApsaraDB RDS for MySQL instance** as an example to describe how to configure a data synchronization task. You can also follow the procedure to configure data synchronization tasks for other types of MySQL databases.

**Note:** If your source database is a user-created MySQL database, you must prepare the environment that is required for the source database. For more information, see [Preparation overview]().

## SQL operations that can be synchronized

-   DDL operation: ADD COLUMN

    **Note:** Only the following data types are supported: INTEGER, BIGINTEGER, IGINT, LONGSTRING, YEAR, TIME, DATA, TIME STAMP, DATA TIME BYTE BOOLEAN DECIMAL, DOUBLE, and FLOAT.

-   Data manipulation language \(DML\) operations: INSERT, UPDATE, and DELETE

## Synchronization process

1.  Initial schema synchronization

    DTS synchronizes the schemas of the required objects from the source database to MaxCompute. During initial schema synchronization, DTS adds the \_base suffix to the end of the source table name. For example, if the name of the source table is customer, the name of the table in MaxCompute is customer\_base.

2.  Initial full data synchronization

    DTS synchronizes the historical data of the table from the source database to the destination table in MaxCompute. For example, the customer table in the source database is synchronized to the customer\_base table in MaxCompute. The data is the basis for subsequent incremental synchronization.

    **Note:** The destination table that is suffixed with \_base is known as a full baseline table.

3.  Incremental data synchronization.

    DTS creates an incremental data table in MaxCompute. The name of the incremental data table is suffixed with \_log, for example, customer\_log. Then, DTS synchronizes the incremental data that was generated in the source database to the incremental data table.

    **Note:** For more information, see [Schema of an incremental data table](#section_5kn_hla_atd).


## Procedure

**Warning:** To ensure that the synchronization account can be authorized, we recommend that you perform the following steps by using your Alibaba Cloud account.

1.  Purchase a data synchronization instance. For more information, see [Purchase a DTS instance]().

    **Note:** On the buy page, set Source Instance to **MySQL**, set Target Instance to **MaxCompute**, and set Synchronization Topology to **One-Way Synchronization**.

2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

3.  In the left-side navigation pane, click **Data Synchronization**.

4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column.

6.  Configure the source and destination instances.

    ![Configure the source and destination instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1130359951/p62745.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|Select **RDS Instance**.|
    |Instance Region|The source region that you selected on the buy page. You cannot change the value of this parameter.|
    |Instance ID|Select the ID of the source RDS instance.|
    |Database Account|Enter the database account of the source RDS instance. **Note:** If the database engine of the source RDS instance is **MySQL 5.5** or **MySQL 5.6**, you do not need to configure the **database account** or **database password**. |
    |Database Password|Enter the password of the source database account.|
    |Encryption|Select **Non-encrypted** or **SSL-encrypted**. If you want to select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before you configure the data synchronization task. For more information, see [Configure SSL encryption on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96120.htm). **Note:** The **Encryption** parameter is available only for regions in mainland China and the Hong Kong \(China\) region. |
    |Destination Instance Details|Instance Type|This parameter is set to **MaxCompute** and cannot be changed.|
    |Instance Region|The destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |Project|Enter the name of the MaxCompute **project**. You can search for a project on the [Workspaces](https://workbench.data.aliyun.com/consolenew#/projectlist) page in the DataWorks console.![MaxCompute projects](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6410529851/p55484.png) |

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelists of the RDS instance and the MaxCompute project. This ensures that DTS servers can connect to the source and destination instances.

8.  In the lower-right corner of the page, click **Next**. In this step, the permissions on the MaxCompute project are granted to the synchronization account.

    ![Grant permissions to the synchronization account](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1130359951/p55487.png)

9.  Select the synchronization policy and the objects to be synchronized.

    ![Select the synchronization policy and the objects to be synchronized](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1130359951/p55488.png)

    |Parameter|Description|
    |:--------|:----------|
    |Partition Definition of Incremental Data Table|Select the partition name based on your business requirements. For more information about partitions, see [Partition](https://www.alibabacloud.com/help/zh/doc-detail/27820.htm).|
    |Initial Synchronization|Initial synchronization includes initial schema synchronization and initial full data synchronization.

Select both **Initial Schema Synchronization** and **Initial Full Data Synchronization**. In this case, DTS synchronizes the schemas and historical data of the required objects and then synchronizes incremental data. |
    |Select the processing mode of conflicting tables|    -   **Pre-check and Intercept**: checks whether the destination database contains tables that have the same names as tables in the source database. If the source and destination databases do not contain identical table names, the precheck is passed. Otherwise, an error is returned during precheck and the data synchronization task cannot be started.

**Note:** You can use the object name mapping feature to change the names of the tables that are synchronized to the destination database. You can use this feature if the source and destination databases contain identical table names and the tables in the destination database cannot be deleted or renamed. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md).

    -   **Ignore**: skips the precheck for identical table names in the source and destination databases.

**Warning:** If you select **Ignore**, data consistency is not guaranteed and your business may be exposed to potential risks.

        -   During initial data synchronization, DTS does not synchronize the data records that have the same primary keys as the data records in the destination database. This occurs if the source and destination databases have the same schema. However, DTS synchronizes these data records during incremental data synchronization.
        -   If the source and destination databases have different schemas, initial data synchronization may fail. In this case, only some columns are synchronized or the data synchronization task fails. |
    |Select the objects to be synchronized|Select tables from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the tables to the Selected section.

**Note:**

    -   You can select tables from multiple databases as the objects to be synchronized.
    -   After an object is synchronized to the destination instance, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are synchronized to the destination database. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md). |

10. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, a precheck is performed. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
11. Close the Precheck dialog box after the following message is displayed: **The precheck is passed**.

12. Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    On the Synchronization Tasks page, view the status of the data synchronization task.

    ![View the status of a data synchronization task.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5130359951/p41059.png)


## Schema of an incremental data table

DTS synchronizes incremental data that is generated in the source MySQL database to the incremental data table in MaxCompute. The incremental data table stores incremental data and specific metadata. The following figure shows the schema of an incremental data table.

![Schema of an incremental data table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2130359951/p55989.png)

**Note:** In the example, the `modifytime_year`, `modifytime_month`, `modifytime_day`, `modifytime_hour`, and `modifytime_minute` fields form the partition key. These fields are specified in the [Select the synchronization policy and the objects to be synchronized](#step_pze_gpg_5gx) step.

Schema of an incremental data table

|Field|Description|
|:----|:----------|
|record\_id|The ID of the incremental log entry. **Note:**

-   The ID auto-increments for each new log entry.
-   If an UPDATE operation is performed, DTS generates two incremental log entries to record the pre-update and post-update values. The two incremental log entries have the same record ID. |
|operation\_flag|The operation type. Valid values: -   I: an INSERT operation
-   D: a DELETE operation
-   U: an UPDATE operation |
|utc\_timestamp|The operation timestamp. It is also the timestamp of the binary log file. The timestamp is in the UTC format.|
|before\_flag|Indicates whether the column values are pre-update values. Valid values: Y and N.|
|after\_flag|Indicates whether the column values are post-update values. Valid values: Y and N.|

## Additional information about the before\_flag and after\_flag fields

The **before\_flag** and **after\_flag** fields of an incremental log entry are defined depending on the operation type.

-   INSERT

    For an INSERT operation, the column values are the newly inserted record values \(post-update values\). The value of the before\_flag field is N and the value of the after\_flag field is Y.

    ![Example: INSERT operation](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2130359951/p55992.png)

-   UPDATE

    DTS generates two incremental log entries for an UPDATE operation. The two incremental log entries have the same values for the record\_id, operation\_flag, and utc\_timestamp fields.

    The first log entry records the pre-update values, so the value of the before\_flag field is Y and the value of the after\_flag field is N. The second log entry records the post-update values, so the value of the before\_flag field is N and the value of the after\_flag field is Y.

    ![Example: UPDATE operation](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2130359951/p55993.png)

-   DELETE

    For a DELETE operation, the column values are the deleted record values \(pre-update values\). The value of the before\_flag field is Y and the value of the after\_flag field is N.

    ![Example: DELETE operation](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2130359951/p55994.png)


## Merge a full baseline table and an incremental data table

After a data synchronization task is started, DTS creates a full baseline table and an incremental data table in MaxCompute. You can use SQL statements to merge the two tables. This allows you to obtain the full data at a specific time point.

This section describes how to merge data for a table named customer. The following figure shows the schema of the customer table.

![Schema of the customer table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2130359951/p56270.png)

1.  Create a table in MaxCompute based on the schema of the source table. The table is used to store the merged data.

    For example, you can obtain full data of the customer table at the `1565944878` time point. Run the following SQL statements to create the required table:

    ```
    CREATE TABLE `customer_1565944878` (
        `id` bigint NULL,
        `register_time` datetime NULL,
        `address` string);
    ```

    **Note:**

    -   You can use the ad-hoc query feature to run SQL statements. For more information, see [\(Optional\) Use an ad-hoc query to run SQL statements](~~142384~~).
    -   For more information about the data types that are supported by MaxCompute, see [Data types](~~27821~~).
2.  Run the following SQL statements in MaxCompute to merge the full baseline table and the incremental data table and obtain full data at a specific time point:

    ```
    set odps.sql.allow.fullscan=true;
    insert overwrite table <result_storage_table>
    select <col1>,
           <col2>,
           <colN>
      from(
    select row_number() over(partition by t.<primary_key_column>
     order by record_id desc, after_flag desc) as row_number, record_id, operation_flag, after_flag, <col1>, <col2>, <colN>
      from(
    select incr.record_id, incr.operation_flag, incr.after_flag, incr.<col1>, incr.<col2>,incr.<colN>
      from <table_log> incr
     where utc_timestamp< <timestamp>
     union all
    select 0 as record_id, 'I' as operation_flag, 'Y' as after_flag, base.<col1>, base.<col2>,base.<colN>
      from <table_base> base) t) gt
    where record_num=1 
      and after_flag='Y'
    ```

    **Note:**

    -   <result\_storage\_table\>: the name of the table that stores the merged data.
    -   <col1\>/<col2\>/<colN\>: the names of the columns in the table to be merged.
    -   <primary\_key\_column\>: the name of the primary key column in the table to be merged.
    -   <table\_log\>: the name of the incremental data table.
    -   <table\_base\>: the name of the full baseline table.
    -   <timestamp\>: the timestamp that is generated when full data is obtained.
    Run the following SQL statements to obtain full data of the customer table at the `1565944878` time point:

    ```
    set odps.sql.allow.fullscan=true;
    insert overwrite table customer_1565944878
    select id,
           register_time,
           address
      from(
    select row_number() over(partition by t.id
     order by record_id desc, after_flag desc) as row_number, record_id, operation_flag, after_flag, id, register_time, address
      from(
    select incr.record_id, incr.operation_flag, incr.after_flag, incr.id, incr.register_time, incr.address
      from customer_log incr
     where utc_timestamp< 1565944878
     union all
    select 0 as record_id, 'I' as operation_flag, 'Y' as after_flag, base.id, base.register_time, base.address
      from customer_base base) t) gt
     where gt.row_number= 1
       and gt.after_flag= 'Y';
    ```

3.  Query the merged data from the customer\_1565944878 table.

    ![Query the merged data](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2130359951/p56256.png)


