# Configure two-way data synchronization between ApsaraDB for Redis Enterprise Edition instances

This topic describes how to configure two-way data synchronization between ApsaraDB for Redis Enterprise Edition instances by using Data Transmission Service \(DTS\). The data synchronization feature is applicable to scenarios such as active geo-redundancy and geo-disaster recovery.

## Prerequisites

The source and destination instances are [ApsaraDB for Redis Enterprise Edition](https://www.alibabacloud.com/help/zh/doc-detail/145957.htm) instances \(version 5.0\).

**Note:** ApsaraDB for Redis Enterprise Edition supports the **cluster**, **standard**, and **read/write splitting** architectures.

## Precautions

-   During two-way data synchronization, the data synchronization task in one direction performs and The data synchronization task in the opposite direction performs only incremental data synchronization.

    **Warning:** To ensure data consistency, do not modify or write data to the same key in the source and destination databases when the two-way data synchronization tasks are running.

-   DTS uses the resources of the source and destination instances during initial full data synchronization. This may increase the loads of the database servers. If you synchronize a large volume of data or the server specifications cannot meet your requirements, the database services may become unavailable. Before you synchronize data, evaluate the impact of data synchronization on the performance of the source and destination databases. We recommend that you synchronize data during off-peak hours.
-   We recommend that you do not run the `FLUSHDB` or `FLUSHALL` command in the source instance during data synchronization. Otherwise, data may become inconsistent between the source and destination instances.
-   If the data eviction policy \(`maxmemory-policy`\) of the destination database is not set to `noeviction`, data may become inconsistent between the source and destination databases. For more information about data eviction policies, see [How does ApsaraDB for Redis evict data by default?](/intl.en-US/User Guide/FAQ/Expiration policy/How does ApsaraDB for Redis evict data by default?.md)
-   If an expiration policy is enabled for some keys in the source database, some keys may not be deleted in a timely manner after they expired. Therefore, the number of keys in the destination database may be less than that in the source database. You can run the info command to view the number of keys in the destination database.

    **Note:** The number of keys that do not have an expiration policy or have not expired is the same in the source and destination databases.

-   If direct connection is disabled for the destination ApsaraDB for Redis instance, DTS uses the proxy forwarding mode to write data to the destination instance.

    **Note:** For more information, see [Enable a direct connection](/intl.en-US/User Guide/Manage instances/Network connection management/Enable a direct connection.md).




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
-   RENAME, RENAMENX, RPOP, RPOPLPUSH, RPUSH, and RPUSHX
-   SADD, SDIFFSTORE, SELECT, SET, SETBIT, SETEX, SETNX, SETRANGE, SINTERSTORE, SMOVE, SPOP, SREM, and SUNIONSTORE
-   ZADD, ZINCRBY, ZINTERSTORE, ZREM, ZREMRANGEBYLEX, ZUNIONSTORE, ZREMRANGEBYRANK, and ZREMRANGEBYSCORE
-   SWAPDB \(This operation is not supported if the source or destination Redis instance is deployed in the cluster architecture.\)

**Note:**

-   If you use the EVAL or EVALSHA command to call Lua scripts, DTS cannot identify whether these Lua scripts are executed on the destination database. During incremental data synchronization, the destination database does not explicitly return the execution results of Lua scripts.
-   When DTS calls the SYNC or PSYNC command to transfer data of the LIST type, DTS does not clear the existing data. In this case, the destination database may contain duplicate data records.

## Permissions required for database accounts

|Database|Permission and authorization method|
|--------|-----------------------------------|
|Source ApsaraDB for Redis instance|The database account must have the read and write permissions. For more information about how to authorize the account, see [Create and manage database accounts](/intl.en-US/User Guide/Security management/Create and manage database accounts.md).|
|Destination ApsaraDB for Redis instance|

## Procedure

1.  Purchase a data synchronization instance. For more information, see [Purchase a DTS instance]().

    **Note:** On the buy page, set Source Instance to **Redis**, set Target Instance to **Redis**, and set Synchronization Topology to **Two-Way Synchronization**.

2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

3.  In the left-side navigation pane, click **Data Synchronization**.

4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Configure the data synchronization task in one direction.

    1.  Find the data synchronization instance, and click **Configure Synchronization Channel** in the **Actions** column of the first data synchronization task.

        **Note:** A two-way data synchronization instance contains two data synchronization tasks. You must configure a channel for each task. When you configure the second data synchronization task, find the task and click **Configure Synchronization Channel** in the **Actions** column.

        ![Two-way data synchronization tasks](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7920359951/p41151.png)

    2.  Configure the source and destination instances.

        ![Configure the source and destination instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8828449951/p98365.png)

        |Section|Parameter|Description|
        |:------|:--------|:----------|
        |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
        |Source Instance Details|Instance Type|Select **Redis Instance**.|
        |Instance Region|The source region that you selected on the buy page. You cannot change the value of this parameter.|
        |Instance ID|Select the ID of the source ApsaraDB for Redis instance. **Note:** When you configure the data synchronization task in the opposite direction, select the ID of the destination ApsaraDB for Redis instance. |
        |Database Password|Enter the database password of the source ApsaraDB for Redis instance. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_rvb_h4w_l8r). **Note:** The format of the database password is <user\>:<password\>. For example, if the username of a custom account is admin and the password is Rp829dlwa, the database password is admin:Rp829dlwa. |
        |Destination Instance Details|Instance Type|Select **Redis Instance**.|
        |Instance Region|The destination region that you selected on the buy page. You cannot change the value of this parameter.|
        |Instance ID|Select the ID of the destination ApsaraDB for Redis instance. **Note:** When you configure the data synchronization task in the opposite direction, select the ID of the source ApsaraDB for Redis instance. |
        |Database Password|Enter the database password of the destination ApsaraDB for Redis instance. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_rvb_h4w_l8r). **Note:** The format of the database password is <user\>:<password\>. For example, if the username of a custom account is admin and the password is Rp829dlwa, the database password is admin:Rp829dlwa. |

    3.  In the lower-right corner of the page, click **Set Whitelist and Next**.

        DTS adds the CIDR blocks of DTS servers to the whitelists of the source and destination ApsaraDB for Redis instances. This ensures that DTS servers can connect to the source and destination instances.

    4.  Select the synchronization policy and the objects to be synchronized.

        ![Select objects for two-way data synchronization between ApsaraDB for Redis instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8828449951/p98367.png)

        |Section|Parameter|Description|
        |:------|---------|:----------|
        |Synchronization policy|Conflict Resolution Policy|**Overwrite** During data synchronization, if data records have the same key but different values, the data record with the latest key value overwrites the conflicting records. |
        |Processing Mode In Existed Target Table|        -   **Pre-check and Intercept**: checks whether the destination database is empty. If the destination database is empty, the precheck is passed. If the database is not empty, an error is returned during the precheck and the data synchronization task cannot be started.
        -   **Ignore**: skips the check for empty destination database.

**Warning:** If you select **Ignore**, the data records in the source database overwrite the data records with the same keys in the destination database. |
        |Select the objects to be synchronized|N/A|        -   Select one or more databases from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the databases to the Selected section.
        -   You can select only databases as the objects to be synchronized. You cannot select keys as the objects to be synchronized. |

    5.  In the lower-right corner of the page, click **Next**.

    6.  Configure initial synchronization.

        ![Configure initial synchronization between ApsaraDB for Redis instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8828449951/p98370.png)

        The value is set to **Include full data + incremental data**. DTS synchronizes historical data from the source ApsaraDB for Redis instance to the destination ApsaraDB for Redis instance. Then, DTS synchronizes incremental data.

        **Note:**

        -   If all the required objects have been synchronized from the source instance to the destination instance, the data synchronization task in the opposite direction synchronizes only incremental data.
        -   If a version-related error message appears, upgrade the source ApsaraDB for Redis instance to a specified version. For more information, see [Upgrade the major version](/intl.en-US/User Guide/Manage instances/Lifecycle management/Upgrade the major version.md) and [Upgrade the minor version](/intl.en-US/User Guide/Manage instances/Lifecycle management/Upgrade the minor version.md).
    7.  In the lower-right corner of the page, click **Precheck**.

        **Note:**

        -   Before you can start the data synchronization task, a precheck is performed. You can start the data synchronization task only after the task passes the precheck.
        -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
    8.  Close the Precheck dialog box after the following message is displayed: **The precheck is passed.** Then, the first data synchronization task starts.

6.  Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    You can view the status of the data synchronization task on the Synchronization Tasks page.

7.  Configure the data synchronization task in the opposite direction.

    1.  Find the second data synchronization task, and click **Configure Synchronization Channel** in the Actions column.

        ![Configure the data synchronization task in the opposite direction](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9828449951/p98382.png)

    2.  Repeat substeps ii to viii that are described in [Step 5](#step_5mc_pl2_4i8).


Wait until both data synchronization tasks are in the **Synchronizing** state.

![Two-way data synchronization configured](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9828449951/p98389.png)

