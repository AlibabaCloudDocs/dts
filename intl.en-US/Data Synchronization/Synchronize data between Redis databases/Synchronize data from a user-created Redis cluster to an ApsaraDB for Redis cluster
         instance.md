# Synchronize data from a user-created Redis cluster to an ApsaraDB for Redis cluster instance

Data Transmission Service \(DTS\) supports one-way data synchronization between two Redis clusters. This feature is applicable to scenarios such as data migration, active geo-redundancy, and geo-disaster recovery. This topic describes how to configure one-way data synchronization from a user-created Redis cluster to an ApsaraDB for Redis cluster instance.

You can also follow the procedure to configure data synchronization from an ApsaraDB for Redis cluster instance to a user-created Redis cluster. However, you must configure parameters for the source and destination instances based on the actual scenarios.

**Warning:** After you configure a data synchronization task, do not change the [architecture type](https://www.alibabacloud.com/help/zh/doc-detail/86132.htm) of the source or destination database. For example, if you change the master-replica architecture to the cluster architecture, data synchronization fails.

## Prerequisites

-   The database version of the user-created Redis cluster is 2.8, 3.0, 3.2, 4.0, or 5.0.

    **Note:** The database version of the destination ApsaraDB for Redis cluster instance can be 2.8, 4.0, or 5.0. The version of the destination database must be the same as or later than the version of the source database. If you want to synchronize data between different versions of Redis databases, make sure that the versions of the source and destination databases are compatible. You can create a pay-as-you-go ApsaraDB for Redis cluster instance to verify database compatibility. After verification, you can release the instance or change the billing method to subscription.

-   The available storage space of the destination ApsaraDB for Redis cluster instance is larger than the total size of the data in the source Redis database.
-   All nodes of the source Redis cluster support the `PSYNC` command and share the same password.

## Precautions

-   DTS uses the resources of the source and destination instances during initial full data synchronization. This may increase the loads of the database servers. If you synchronize a large volume of data or the server specifications cannot meet your requirements, database services may become unavailable. Before you synchronize data, evaluate the impact of data synchronization on the performance of the source and destination databases. We recommend that you synchronize data during off-peak hours.
-   We recommend that you increase the value of the repl-backlog-size parameter in the `redis.conf` file. This ensures the stability of data synchronization.
-   To ensure the synchronization quality, DTS adds the following key to the source Redis database: DTS\_REDIS\_TIMESTAMP\_HEARTBEAT. This key is used to record the time when data is synchronized to ApsaraDB for Redis.
-   We recommend that you do not run the `FLUSHDB` or `FLUSHALL` command in the source Redis cluster. Otherwise, data in the source and destination databases may become inconsistent.
-   If the data eviction policy \(`maxmemory-policy`\) of the destination instance is not set to `noeviction`, data may become inconsistent between the source and destination instances. For more information about the data eviction policy, see [How does ApsaraDB for Redis evict data by default?](/intl.en-US/User Guide/FAQ/Expiration policy/How does ApsaraDB for Redis evict data by default?.md)

## Supported synchronization topologies

-   One-way one-to-one synchronization
-   One-way one-to-many synchronization
-   One-way cascade synchronization

For more information about synchronization topologies, see [Synchronization topologies](/intl.en-US/Data Synchronization/Synchronization topologies.md).

## Operations that can be synchronized

-   APPEND
-   BITOP, BLPOP, BRPOP, and BRPOPLPUSH
-   DECR, DECRBY, and DEL
-   EVAL, EVALSHA, EXEC, EXPIRE, and EXPIREAT
-   GEOADD and GETSET
-   HDEL, HINCRBY, HINCRBYFLOAT, HMSET, HSET, and HSETNX
-   INCR, INCRBY, and INCRBYFLOAT
-   LINSERT, LPOP, LPUSH, LPUSHX, LREM, LSET, and LTRIM
-   MOVE, MSET, MSETNX, and MULTI
-   PERSIST, PEXPIRE, PEXPIREAT, PFADD, PFMERGE, PSETEX, and PUBLISH
-   RENAME, RENAMENX, RESTORE, RPOP, RPOPLPUSH, RPUSH, and RPUSHX
-   SADD, SDIFFSTORE, SELECT, SET, SETBIT, SETEX, SETNX, SETRANGE, SINTERSTORE, SMOVE, SPOP, SREM, and SUNIONSTORE
-   ZADD, ZINCRBY, ZINTERSTORE, ZREM, ZREMRANGEBYLEX, ZUNIONSTORE, ZREMRANGEBYRANK, and ZREMRANGEBYSCORE
-   SWAPDB and UNLINK \(supported only if the database version of the source ApsaraDB for Redis instance is 4.0\)

**Note:**

-   If you run the EVAL or EVALSHA command to call Lua scripts, DTS cannot identify whether these Lua scripts are executed on the destination database. During incremental data synchronization, the destination database does not explicitly return the execution results of Lua scripts.
-   When DTS calls the SYNC or PSYNC command to transfer data of the LIST type, DTS does not clear the existing data. In this case, the destination database may contain duplicate data records.

## Procedure

1.  [Purchase a data synchronization instance]().

    **Note:** On the buy page, set both Source Instance and Target Instance to **Redis**.

2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).
3.  In the left-side navigation pane, click **Data Synchronization**.
4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column.
6.  Configure the source and destination instances.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7928449951/p54591.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|Select **User-Created Database in ECS Instance**. You can select **User-Created Database in ECS Instance** or **User-Created Database Connected Over Express Connect, VPN Gateway, or Smart Access Gateway** based on the type of the source database. The procedure in this topic uses **user-created database hosted on ECS** as an example. You can also follow the procedure to configure data synchronization for other types of user-created Redis databases. |
    |Instance Region|The source region that you selected on the buy page. You cannot change the value of this parameter.|
    |ECS Instance ID|Select the ID of the ECS instance where a master node in the user-created Redis cluster resides.|
    |Database Type|The value of this parameter is set to **Redis**.|
    |Instance Mode|Select **Cluster**.|
    |Port Number|Enter the service port number of a master node in the user-created Redis cluster. In this example, enter **7000**.|
    |Database Password|Enter the password that is used to log on to the user-created Redis database. **Note:** This parameter is optional and can be left blank if no database password is set. |
    |Destination Instance Details|Instance Type|Select **Redis Instance**.|
    |Instance Region|The destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |Instance ID|Select the ID of the destination ApsaraDB for Redis cluster instance.|
    |Database Password|Enter the database password of the destination ApsaraDB for Redis cluster instance. **Note:** The format of the database password is <user\>:<password\>. For example, if the username of the custom account is admin and the password is Rp829dlwa, the database password is admin:Rp829dlwa. |

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the inbound rule of the source ECS instance and the whitelist of the destination ApsaraDB for Redis cluster instance. This ensures that DTS servers can connect to the source and destination instances.

8.  Select the processing mode of conflicting tables, and the objects to be synchronized.

    ![Select the processing mode and objects](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7928449951/p50649.png)

    |Setting|Description|
    |:------|:----------|
    |Select the processing mode of conflicting tables|    -   **Pre-check and Intercept**: checks whether the destination database is empty. If the destination database is empty, the precheck is passed. If the database is not empty, an error is returned during the precheck and the data synchronization task cannot be started.
    -   **Ignore**: skips the check for empty destination databases.

**Warning:** If you select **Ignore**, the data records in the source database overwrite the data records that have the same keys in the destination database. |
    |Select the objects to be synchronized|    -   Select one or more databases from the Available section and click the ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the databases to the Selected section.
    -   You can select only databases as the objects to be synchronized. You cannot select keys as the objects to be synchronized. |

9.  In the lower-right corner of the page, click **Next**.
10. Configure initial synchronization. The value is set to **Include full data + incremental data**.

    ![Configure initial synchronization](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4038449951/p50606.png)

    **Note:**

    -   DTS synchronizes historical data from the source ApsaraDB for Redis instance to the destination ApsaraDB for Redis instance. Then, DTS synchronizes incremental data.
    -   If a version-related error message appears, upgrade the source ApsaraDB for Redis instance to a specified version. For more information, see [Upgrade the major version](/intl.en-US/User Guide/Manage instances/Lifecycle management/Upgrade the major version.md) and [Upgrade the minor version](/intl.en-US/User Guide/Manage instances/Lifecycle management/Upgrade the minor version.md).
11. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, DTS performs a precheck. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run a precheck again.
12. Close the Precheck dialog box after the following message appears: **The precheck is passed.** Then, the data synchronization task starts.
13. Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    ![Status of the data synchronization task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5130359951/p41059.png)

    **Note:** You can view the status of the data synchronization task on the Synchronization Tasks page.


