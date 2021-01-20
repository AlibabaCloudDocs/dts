# FAQ

This topic provides answers to frequently asked questions \(FAQ\) about Data Transmission Service \(DTS\).

-   General
    -   [Which databases do DTS tasks support in data migration, data synchronization, and change tracking mode?](#section_v9f_0z8_whg)
    -   [What are the differences between incremental data migration and data synchronization?](#section_pwx_f7n_ca6)
    -   [How is the synchronization delay measured?](#section_572_2op_1yd)
    -   [How do I consume tracked data by using a task in change tracking mode?](#section_f8d_xyg_c8q)
-   Billing
    -   [How is DTS billed?](#section_fia_pq1_hyc)
    -   [Why is the price of data synchronization higher than that of data migration?](#section_4q5_s6f_dyl)
-   Instance sizes
    -   [What are the differences in performance between different instance sizes?](#section_3b6_zjm_z2q)
    -   [Can instance sizes be downgraded and upgraded?](#section_kya_ezc_96o)
-   Features
    -   [Can I migrate or synchronize data between Alibaba Cloud accounts?](#section_d1p_8z3_vcj)
    -   [Can I migrate data within a single instance?](#section_a6v_ggy_6i8)
    -   [Can I migrate or synchronize DML and DDL operations?](#section_s3v_d13_266)
    -   [Can I migrate or synchronize database shards and table shards?](#section_m5c_gol_l0x)
    -   [Can I migrate or synchronize data across time zones and character sets?](#section_mxv_mpr_vem)
    -   [Can I change the name of objects that are migrated or synchronized to the target database?](#section_ux1_d9z_32i)
    -   [Can I change the name of objects that are migrated or synchronized to the target database?](#section_ux1_d9z_32i)
    -   [Can I filter columns and rows?](#section_of8_uhd_l1v)
    -   [Can I add or remove objects to be synchronized?](#section_h5e_b9y_x6b)
-   Performance
    -   [How do I view the performance metrics of data migration and synchronization tasks?](#section_k1m_lqx_5fc)

## Which databases do DTS tasks support in data migration, data synchronization, and change tracking mode?

DTS supports data transmission between various data sources, such as relational database management systems \(RDBMS\), NoSQL databases, and online analytical processing \(OLAP\) databases. For more information, see [Supported sources and targets](/intl.en-US/Introduction/Supported sources and targets.md).

**Note:** DTS also supports data migration and synchronization between databases that are provided by third-party cloud vendors. For more information, see [Overview of data migration scenarios](/intl.en-US/Data Migration/Overview of data migration scenarios.md).

## What are the differences between incremental data migration and data synchronization?

The data synchronization mode and the incremental data migration phase of the data migration mode offer seemingly similar functionality. They both perform continuous replications. However, they have different focuses.

The incremental data migration phase of the data migration mode is intended for minimized-downtime migrations. While DTS loads the existing data of the source database, updates are still being made to the source database. The incremental data migration is to ensure the updates occurring during this period are eventually applied to the target database.

The data synchronization mode is intended for continuous replications. It is usually used for data replication between databases in a distributed system, for example, for redundancy and high availability. Compared with the incremental data migration phase of data migration, the data synchronization mode delivers higher performance and lower lag.

## How is the synchronization delay measured?

The synchronization delay is the difference between the timestamp of the latest synchronized data in the target database and the current timestamp in the source database. The unit is milliseconds.

## How do I consume tracked data by using a task in change tracking mode?

You can use a Kafka client to consume tracked data. For more information, see [Use a Kafka client to consume tracked data](/intl.en-US/Change Tracking/Change tracking (new)/Use a Kafka client to consume tracked data.md)

## How is DTS billed?

DTS provides two billing methods: subscription and pay-as-you-go. Change tracking and data synchronization instances support both billing methods. Data migration mode instances only support pay-as-you-go.

The subscription billing method allows you to pay for your subscription when creating an instance. Subscription lengths are monthly or yearly, and you can renew the subscription either manually or automatically. We recommend that you select subscription billing if you plan to use DTS for a period lasting a month or longer.

The pay-as-you-go billing method allows you to pay for your DTS instance based on the time your tasks are running. A pay-as-you-go instance is billed on an hourly basis, and you can release a pay-as-you-go instance at any time. We recommend that you select this billing method for short term use.

For more information about billing methods, see [Billing methods](/intl.en-US/Billing/Billing methods.md). For details about how DTS is priced, see the [Pricing](https://www.alibabacloud.com/product/data-transmission-service/pricing) page.

## Why is the price of data synchronization higher than that of data migration?

Data synchronization mode comes with more advanced features. For example, you can modify the objects to be synchronized. You can configure two-way data synchronization between MySQL databases. In addition, the data synchronization mode ensures low network latency through data transmission over the internal network.

## What are the differences in performance between different instance sizes?

The maximum performance of data migration and data synchronization tasks depends on the instance size.

For information about how different instance sizes perform in different scenarios, see Data migration mode test data and Data synchronization mode test data.

## Can instance sizes be downgraded and upgraded?

You can upgrade the instance size of instances in data migration or data synchronization mode. You can upgrade an instance size by finding it in the instance list, and then clicking **Upgrade** in the **Actions** column. Instance sizes cannot be downgraded.

## Can I migrate or synchronize data between Alibaba Cloud accounts?

-   Data migration mode: You can migrate data across Alibaba Cloud accounts between ApsaraDB RDS for MySQL instances. For more information, see [Migrate data between RDS instances across Alibaba Cloud accounts](/intl.en-US/Data Migration/Migrate data across Alibaba Cloud accounts/Migrate data between RDS instances across Alibaba Cloud accounts.md). You can also migrate data across Alibaba Cloud accounts between other database instances, such as Apsara PolarDB for MySQL, DRDS, Redis, and MongoDB. To do this, you can specify the database as a User-created Database with a Public IP Address when you configure the data migration task.
-   Data synchronization mode: You can synchronize data across Alibaba Cloud accounts only between ApsaraDB RDS for MySQL instances. For more information, see [Synchronize data between ApsaraDB RDS for MySQL instances that belong to different Alibaba Cloud accounts](/intl.en-US/Data Synchronization/Synchronize data between MySQL databases/Synchronize data between ApsaraDB RDS for MySQL instances that belong to different
         Alibaba Cloud accounts.md).

## Can I migrate data within a single instance?

Yes, you can migrate data within a single instance. For more information, see [Migrate data between databases with different names](/intl.en-US/Best Practices/Migrate data between databases that have different names.md).

## Can I migrate or synchronize DML and DDL operations?

Yes, you can migrate or synchronize DML and DDL operations between relational databases. The supported DML operations are INSERT, UPDATE, and DELETE. The supported DDL operations are CREATE, DROP, ALTER, RENAME, and TRUNCATE.

**Note:** The supported DML and DDL operations are different in different scenarios. For example, if you synchronize data from a MySQL database to AnalyticDB for MySQL 2.0, only the following DDL operations are supported: CREATE TABLE, ALTER TABLE, and DROP TABLE. Only the following DML operations are supported: INSERT, UPDATE, and DELETE.

## Can I migrate or synchronize database shards and table shards?

Yes, you can migrate or synchronize database shards and table shards. For example, you can migrate or synchronize database shards and table shards from a MySQL database and Apsara PolarDB for MySQL cluster to AnalyticDB for MySQL. This allows you to merge multiple tables.

## Can I migrate or synchronize data across time zones and character sets?

Yes, you can migrate or synchronize data across time zones and character sets.

## Can I change the name of objects that are migrated or synchronized to the target database?

Yes, you can change the name of columns, tables, and databases by using the object name mapping feature. For more information, see [Object name mapping](/intl.en-US/Data Migration/Migration task management/Object name mapping.md).

## Can I filter columns and rows?

Yes, you can filter columns and rows in a table. For more information, see [Use conditions to filter data](/intl.en-US/Data Migration/Migration task management/Use conditions to filter data.md).

## Can I add or remove objects to be synchronized?

Yes, you can add or remove objects to be synchronized. For more information, see [Add an object to a data synchronization task](/intl.en-US/Data Synchronization/Synchronization task management/Add an object to a data synchronization task.md) and [Remove an object from a data synchronization task](/intl.en-US/Data Synchronization/Synchronization task management/Remove an object from a data synchronization task.md).

## How do I view the performance metrics of data migration and synchronization tasks?

You can view performance metrics of data migration and data synchronization tasks in the DTS console. For information about how to view the metrics, see [View the connection status and performance of incremental data migration](/intl.en-US/Data Migration/Migration task management/View the connection status and performance of incremental data migration.md) and [View the connection status and performance of data synchronization](/intl.en-US/Data Synchronization/Synchronization task management/View the connection status and performance of data synchronization.md).

