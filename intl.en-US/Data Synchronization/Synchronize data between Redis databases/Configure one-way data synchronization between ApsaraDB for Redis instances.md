# Configure one-way data synchronization between ApsaraDB for Redis instances

Data Transmission Service \(DTS\) supports one-way data synchronization between ApsaraDB for Redis instances. This feature is applicable to scenarios such as active geo-redundancy and geo-disaster recovery. This topic describes how to configure one-way data synchronization between ApsaraDB for Redis instances.

**Warning:** After you configure a data synchronization task, do not change the [architecture type](https://www.alibabacloud.com/help/zh/doc-detail/86132.htm) of the source or destination database. For example, if you change the master-replica architecture to the cluster architecture, data synchronization fails.

## Prerequisites

The available storage space of the destination ApsaraDB for Redis instance is larger than the total size of the data in the source ApsaraDB for Redis instance.

## Precautions

-   DTS uses the resources of the source and destination databases during initial full data synchronization. This may increase the loads of the database servers. If you synchronize a large volume of data or the server specifications cannot meet your requirements, database services may become unavailable. Before you synchronize data, evaluate the impact of data synchronization on the performance of the source and destination databases. We recommend that you synchronize data during off-peak hours.
-   If an expiration policy is enabled for some keys in the source database, some keys may not be deleted in a timely manner after they expired. Therefore, the number of keys in the destination database may be less than that in the source database. You can run the info command to view the number of keys in the destination database.

    **Note:** The number of keys that do not have an expiration policy or have not expired is the same in the source and destination databases.

-   If the database version of the source ApsaraDB for Redis instance is 2.8, DTS does not synchronize incremental data.
-   We recommend that you do not run the `FLUSHDB` or `FLUSHALL` command on the source database during data synchronization. If you run one of the two commands, data may become inconsistent between the source and destination databases.
-   If the data eviction policy \(`maxmemory-policy`\) of the destination database is not set to `noeviction`, data may become inconsistent between the source and destination databases. For more information about data eviction policies, see [How does ApsaraDB for Redis evict data by default?](/intl.en-US/User Guide/FAQ/Expiration policy/How does ApsaraDB for Redis evict data by default?.md)

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

## Permissions required for database accounts

|Database|Permission and authorization method|
|--------|-----------------------------------|
|Source ApsaraDB for Redis instance|The database account must have the read permission. For more information about how to authorize the account, see [Create and manage database accounts](/intl.en-US/User Guide/Security management/Create and manage database accounts.md).|
|Destination ApsaraDB for Redis instance|The database account must have the read and write permissions. For more information about how to authorize the account, see [Create and manage database accounts](/intl.en-US/User Guide/Security management/Create and manage database accounts.md).|

## Procedure

1.  Purchase a data synchronization instance. For more information, see [Purchase a data synchronization instance]().

    **Note:** On the buy page, set both Source Instance and Target Instance to **Redis**.

2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).
3.  In the left-side navigation pane, click **Data Synchronization**.
4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column.
6.  Configure the source and destination instances.

    ![Configure the source and destination instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0928449951/p50605.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|Select **Redis Instance**.|
    |Instance Region|The source region that you selected on the buy page. You cannot change the value of this parameter.|
    |Instance ID|Select the ID of the source ApsaraDB for Redis instance.|
    |Database Password|Enter the database password of the source ApsaraDB for Redis instance. For more information about the permissions that are required for the database account, see [Permissions required for database accounts](#section_3mt_iy9_yua).**Note:** The format of the database password is <user\>:<password\>. For example, if the username of a custom account is admin and the password is Rp829dlwa, the database password is admin:Rp829dlwa. |
    |Destination Instance Details|Instance Type|Select **Redis Instance**.|
    |Instance Region|The destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |Instance ID|Select the ID of the destination ApsaraDB for Redis instance.|
    |Database Password|Enter the database password of the destination ApsaraDB for Redis instance. For more information about the permissions that are required for the database account, see [Permissions required for database accounts](#section_3mt_iy9_yua).**Note:** The format of the database password is <user\>:<password\>. For example, if the username of a custom account is admin and the password is Rp829dlwa, the database password is admin:Rp829dlwa. |

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelists of the source and destination ApsaraDB for Redis instances. This ensures that DTS servers can connect to the source and destination ApsaraDB for Redis instances.

8.  Select the processing mode of conflicting tables, and the objects to be synchronized.

    ![Select the processing mode and objects](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7928449951/p50649.png)

    |Setting|Description|
    |:------|:----------|
    |Select the processing mode of conflicting tables|    -   **Pre-check and Intercept**: checks whether the destination database is empty. If the destination database is empty, the precheck is passed. If the database is not empty, an error is returned during the precheck and the data synchronization task cannot be started.
    -   **Ignore**: skips the check for empty destination database.

**Warning:** If you select **Ignore**, the data records in the source database overwrite the data records that have the same keys in the destination database. |
    |Select the objects to be synchronized|    -   Select one or more databases from the Available section and click the ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the databases to the Selected section.
    -   You can select only databases as the objects to be synchronized. You cannot select keys as the objects to be synchronized. |

9.  In the lower-right corner of the page, click **Next**.
10. Configure initial synchronization. The value is set to **Include full data + incremental data**.

    ![Configure initial synchronization](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4038449951/p50606.png)

    **Note:**

    -   DTS synchronizes historical data from the source ApsaraDB for Redis instance to the destination ApsaraDB for Redis instance. Then, DTS synchronizes incremental data.
    -   If a version-related error message appears, you can upgrade the source ApsaraDB for Redis instance to a specified version. For more information, see [Upgrade the major version](/intl.en-US/User Guide/Manage instances/Lifecycle management/Upgrade the major version.md) and [Upgrade the minor version](/intl.en-US/User Guide/Manage instances/Lifecycle management/Upgrade the minor version.md).
11. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, DTS performs a precheck. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, you can click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. You can troubleshoot the issues based on the causes and run a precheck again.
12. Close the Precheck dialog box after the following message appears: **The precheck is passed.** Then, the data synchronization task starts.
13. Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    ![Status of the data synchronization task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5130359951/p41059.png)

    **Note:** You can view the status of the data synchronization task on the Synchronization Tasks page.


