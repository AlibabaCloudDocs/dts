# Specify the policy for synchronizing data to Kafka partitions

When you configure a task to synchronize data to a Kafka cluster, you can specify the policy for synchronizing data to Kafka partitions. The policy allows you to improve the synchronization performance. For example, you can synchronize data to different partitions based on hash values.

## Hash algorithm

Data Transmission Service \(DTS\) uses the hashCode\(\) method in Java to calculate hash values.

## Configuration method

In the **Select Objects to Synchronize** step of a task creating wizard, you can specify the policy for synchronizing data to Kafka partitions. For more information, see [Synchronize data from an ApsaraDB RDS for MySQL instance to a user-created Kafka cluster](/intl.en-US/Data Synchronization/Synchronize data from MySQL databases to other databases/Synchronize data from an ApsaraDB RDS for MySQL instance to a user-created Kafka cluster.md) and [Overview of data synchronization scenarios](/intl.en-US/Data Synchronization/Overview of data synchronization scenarios.md).

**Warning:** After a data synchronization task is started, do not change the number of partitions in the destination topic. Otherwise, data synchronization fails.

## Synchronization policies

|Policy|Description|Advantage and disadvantage|
|------|-----------|--------------------------|
|**Synchronize All Data to Partition 0**|DTS synchronizes all data and DDL statements to Partition 0 of the destination topic.|-   Advantage: The order in which all objects are created and changed is the same as that in the source database.
-   Disadvantage: This policy provides ordinary synchronization performance. |
|**Synchronize Data to Separate Partitions Based on Hash Values of Database and Table Names**|DTS uses the database and table names as the partition key to calculate the hash value. Then, DTS synchronizes the data and DDL statements of each table to the corresponding partition of the destination topic. **Note:**

-   The data and DDL statements of the same table are synchronized to the same partition.
-   If a DDL statement is irrelevant to a table, for example, CREATE DATABASE, the statement is synchronized to Partition 0.

|-   Advantage: The order in which a destination table is created and changed is the same as that of the source table. This policy provides good synchronization performance.
-   Disadvantage: Tables are synchronized to different partitions. After data synchronization, the order of data changes on different tables may become inconsistent. |
|**Synchronize Data to Separate Partitions Based on Hash Values of Primary Keys**|DTS uses a table column as the partition key to calculate the hash value. The table column is the primary key by default. If a table does not have a primary key, the unique key is used as the partition key. DTS synchronizes each row to the corresponding partition of the destination topic. You can specify one or more columns as partition keys to calculate the hash value. **Note:**

-   If you use this policy, DDL statements are synchronized to Partition 0 of the destination topic by default.
-   If a table does not have a primary key or unique key, DTS synchronizes the data and DDL statements of the table to Partition 0 of the destination topic.

|-   Advantage: This policy provides the best synchronization performance.
-   Disadvantage: After data synchronization, the order of data changes on each data record remains the same. However, the order of data changes on different tables or tables without a primary key may become inconsistent. |

