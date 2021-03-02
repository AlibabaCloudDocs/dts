# Migrate data from a user-created Redis database to an ApsaraDB for Redis instance

This topic describes how to migrate data from a user-created Redis database to an ApsaraDB for Redis instance by using Data Transmission Service \(DTS\). DTS supports full data migration and incremental data migration. When you migrate data from a user-created Redis database to Alibaba Cloud, you can select the two migration types to ensure service continuity.

## Prerequisites

-   The version of the user-created Redis database is 2.8, 3.0, 3.2, 4.0, or 5.0.
-   The user-created Redis database is deployed in the standalone architecture rather than the cluster architecture.

    **Note:** If the user-created Redis database is deployed in the cluster architecture, you can migrate data by using the data synchronization feature. For more information, see [Synchronize data from a user-created Redis cluster to an ApsaraDB for Redis cluster instance](/intl.en-US/Data Synchronization/Synchronize data between Redis databases/Synchronize data from a user-created Redis cluster to an ApsaraDB for Redis cluster
         instance.md).

-   The `PSYNC` or `SYNC` command can be run on the user-created Redis database.
-   The available storage space of the destination ApsaraDB for Redis database is larger than the total size of the data in the user-created Redis database.

## Precautions

-   DTS consumes resources of the source and destination databases during full data migration. This may increase the loads on database servers. If you migrate a large volume of data or the server specifications cannot meet your requirements, database services may become unavailable. Before you migrate data, evaluate the impact of data migration on the performance of the source and destination databases. We recommend that you migrate data during off-peak hours.
-   If the data eviction policy \(`maxmemory-policy`\) of the destination database is not set to `noeviction`, data may become inconsistent between the source and destination databases. For more information about data eviction policies, see [How does ApsaraDB for Redis evict data by default?](/intl.en-US/User Guide/FAQ/Expiration policy/How does ApsaraDB for Redis evict data by default?.md)
-   If an expiration policy is enabled for some keys in the source database, some keys may not be deleted in a timely manner after they expired. Therefore, the number of keys in the destination database may be less than that in the source database. You can run the info command to view the number of keys in the destination database.

    **Note:** The number of keys that do not have an expiration policy or have not expired is the same in the source and destination databases.

-   If you run the EVAL or EVALSHA command to call Lua scripts, DTS cannot identify whether these Lua scripts are executed on the destination database. During incremental data migration, the destination database does not explicitly return the execution results of Lua scripts.
-   When you run the `PSYNC` or `SYNC` command to transfer data of the LIST type, DTS does not perform the `flush` operation on the existing data. Therefore, the destination database may contain duplicate data records.
-   If a data migration task fails, DTS automatically resumes the task. Before you switch your workloads to the destination instance, stop or release the data migration task. Otherwise, the data in the source database will overwrite the data in the destination instance after the task is resumed.

## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|--------------|--------------------------|--------------------|
|Schema migration and full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|
|Incremental data migration|Charged. For more information, see [Pricing]().|

## Migration types

-   Full data migration

    DTS migrates historical data of the required objects from the user-created Redis database to the destination ApsaraDB for Redis instance.

    **Note:** To ensure data consistency, do not write new data to the user-created Redis database during full data migration.

-   Incremental data migration

    After full data migration is complete, DTS synchronizes incremental data from the user-created Redis database to the destination ApsaraDB for Redis instance. Incremental data migration allows you to ensure service continuity when you migrate data from a user-created Redis database to Alibaba Cloud.


## Operations that can be synchronized during incremental data migration

-   APPEND
-   BITOP, BLPOP, BRPOP, and BRPOPLPUSH
-   DECR, DECRBY, and DEL
-   EVAL, EVALSHA, EXEC, EXPIRE, and EXPIREAT
-   FLUSHALL and FLUSHDB
-   GEOADD and GETSET
-   HDEL, HINCRBY, HINCRBYFLOAT, HMSET, HSET, and HSETNX
-   INCR, INCRBY, and INCRBYFLOAT
-   LINSERT, LPOP, LPUSH, LPUSHX, LREM, LSET, and LTRIM
-   MOVE, MSET, MSETNX, and MULTI
-   PERSIST, PEXPIRE, PEXPIREAT, PFADD, PFMERGE, PSETEX, and PUBLISH
-   RENAME, RENAMENX, RESTORE, RPOP, RPOPLPUSH, RPUSH, and RPUSHX
-   SADD, SDIFFSTORE, SELECT, SET, SETBIT, SETEX, SETNX, SETRANGE, SINTERSTORE, SMOVE, SPOP, SREM, and SUNIONSTORE
-   ZADD, ZINCRBY, ZINTERSTORE, ZREM, ZREMRANGEBYLEX, ZUNIONSTORE, ZREMRANGEBYRANK, and ZREMRANGEBYSCORE

## Preparations before incremental data migration

To ensure that incremental data migration tasks run as expected, we recommend that you remove the limit on the replication output buffer. This topic uses a server that runs on Linux as an example.

**Note:** If you perform only full data migration, skip the following steps.

1.  Use the redis-cli program to connect to the user-created Redis database.

    **Note:** You can use the redis-cli program after you install the Redis client. For more information, visit [Redis community official website](https://redis.io/download#installation).

    ```
    redis-cli -h <host> -p <port> -a <password>
    ```

    **Note:**

    -   <host\>: the endpoint that is used to connect to the user-created Redis database. You can use 127.0.0.1 in this example.
    -   <port\>: the service port number of the user-created Redis database. The default port number is 6379.
    -   <password\>: the password of the user-created Redis database.
    Example:

    ```
    redis-cli -h 127.0.0.1 -p 6379 -a Test123456
    ```

2.  Run the following command to remove the limit on the replication output buffer:

    ```
    config set client-output-buffer-limit 'slave 0 0 0'
    ```


## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).
2.  In the left-side navigation pane, click **Data Migration**.
3.  At the top of the Migration Tasks page, select the region where the destination RDS instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.
5.  Configure the source and destination databases.

    ![Configure the source and destination databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4924948951/p48773.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify a name that can describe your business for easy identification. You do not need to use a unique task name.|
    |Source Database|Instance Type|Select an instance type based on the deployment of the source database. In this example, select **User-Created Database with Public IP Address**. **Note:** If you select other instance types, you must prepare the environment that is required for the source database. For more information, see [Preparation overview](). |
    |Instance Region|If the instance type is set to **User-Created Database with Public IP Address**, you do not need to specify the **instance region**. **Note:** If a whitelist is configured for the user-created Redis database, you must add the CIDR blocks of DTS servers to the whitelist of the database. You can click **Get IP Address Segment of DTS** next to **Instance Region** to obtain the CIDR blocks of DTS servers. |
    |Database Type|Select **Redis**.|
    |Instance Mode|The value of this parameter is set to **Standalone** and cannot be changed to Cluster.|
    |Hostname or IP Address|Enter the endpoint that is used to connect to the user-created Redis database. In this example, enter the public IP address.|
    |Port Number|Enter the service port number of the user-created Redis database. The default port number is **6379**.**Note:** The service port of the user-created Oracle database must be accessible over the Internet. |
    |Database Password|Enter the password of the user-created Redis database. **Note:** After you specify the source database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
    |Destination Database|Instance Type|Select **Redis Instance**.|
    |Instance Region|Select the region where the destination ApsaraDB for Redis instance resides.|
    |Redis Instance ID|Select the ID of the destination ApsaraDB for Redis instance.|
    |Database Password|Enter the database password of the destination ApsaraDB for Redis instance. **Note:** After you specify the destination database parameters, click **Test Connectivity** next to **Database Password** to verify whether the parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the destination database parameters based on the check results. |

6.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelist of the destination ApsaraDB for Redis instance. This ensures that DTS servers can connect to the destination ApsaraDB for Redis instance.

7.  Select the migration types and the objects to be migrated.

    ![Select the migration types and the objects to be migrated](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4924948951/p48776.png)

    |Setting|Description|
    |:------|:----------|
    |Select the migration types|    -   To perform only full data migration, select only **Full Data Migration**.
    -   To ensure service continuity during data migration, select both **Full Data Migration** and **Incremental Data Migration**.
**Note:** If **Incremental Data Migration** is not selected, do not write data to the user-created Redis database during full data migration. This ensures data consistency between the source and destination databases. |
    |Select the objects to be migrated|Select one or more objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section.

**Note:** You can select databases as the objects to be migrated. |

8.  In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data migration task, a precheck is performed. You can start the data migration task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
9.  After the task passes the precheck, click **Next**.
10. In the Confirm Settings dialog box, specify the **Channel Specification** and select **Data Transmission Service \(Pay-As-You-Go\) Service Terms**.
11. Click **Buy and Start** to start the migration task.
    -   Full data migration

        Do not manually stop a task during full data migration. Otherwise, data migrated to the destination database will be incomplete. Wait until the migration task automatically stops.

    -   Incremental data migration

        An incremental data migration task does not automatically stop. You must manually stop the migration task.

        **Note:** Select an appropriate time to manually stop the migration task. For example, you can stop the migration task during off-peak hours or before you switch your workloads to the destination instance.

        1.  Wait until **Incremental Data Migration** and **The migration task is not delayed** appear in the progress bar of the migration task. Then, stop writing data to the source database for a few minutes. The delay time of **incremental data migration** may be displayed in the progress bar.
        2.  After the status of **incremental data migration** changes to **The migration task is not delayed**, manually stop the migration task.

            ![The migration task is not delayed](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2940359951/p47604.png)

12. Switch your workloads to the destination ApsaraDB for Redis instance.

## What to do next

The database accounts used for data migration have the read and write permissions. After the data migration is complete, you must delete the accounts of both the user-created Redis database and the ApsaraDB for Redis instance to ensure database security.

