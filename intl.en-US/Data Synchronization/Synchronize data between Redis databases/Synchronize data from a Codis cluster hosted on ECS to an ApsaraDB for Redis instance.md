# Synchronize data from a Codis cluster hosted on ECS to an ApsaraDB for Redis instance

ApsaraDB for Redis is a database service compatible with the open source Redis protocol and provides hybrid storage of memory and disks. Based on reliable hot standby architectures and scalable cluster architectures, ApsaraDB for Redis is suitable for scenarios that require flexible configuration changes, high throughput, and low latency. This topic describes how to synchronize data from a Codis cluster to an ApsaraDB for Redis instance by using Data Transmission Service \(DTS\).

-   An ApsaraDB for Redis instance is created and the database version is 2.8, 4.0, or 5.0. For more information, see [Create an ApsaraDB for Redis instance](/intl.en-US/Quick Start/Step 1: Create an ApsaraDB for Redis instance.md).
-   The available storage space of the destination ApsaraDB for Redis instance is larger than the total size of data stored in the source Codis cluster.
-   All master nodes in the source Codis cluster support the `PSYNC` command.

## How DTS synchronizes data from a Codis cluster

A Codis cluster consists of multiple Codis-Groups. You must create a data synchronization task for each Codis-Group. DTS synchronizes each Codis-Group in a data synchronization task until the whole cluster is synchronized.

![Synchronize data from a Codis cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0038449951/p71232.png)

## Architecture of the Codis cluster

In this topic, the Codis cluster consists of two Codis-Groups. Each Codis-Group runs in a master-replica architecture. The following figure shows the architecture of the cluster.

![Architecture of a Codis cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0038449951/p71227.png)

## Precautions

-   DTS uses the resources of the source and destination databases during initial full data synchronization. This may increase the loads of the database servers. If you synchronize a large volume of data or the server specifications cannot meet your requirements, database services may become unavailable. Before you synchronize data, evaluate the impact of data synchronization on the performance of the source and destination databases. We recommend that you synchronize data during off-peak hours.
-   If the `bind` parameter is configured in the redis.conf file of the source database, you must set the value of this parameter to the internal IP address of the ECS instance. The setting ensures that DTS can connect to the source database.
-   We recommend that you increase the value of the `repl-backlog-size` parameter in the redis.conf file. This ensures the stability of data synchronization.
-   To ensure the synchronization quality, DTS adds the following key to the source Codis cluster: `DTS_REDIS_TIMESTAMP_HEARTBEAT`. This key is used to record the time when data is synchronized to ApsaraDB for Redis.
-   We recommend that you do not run the `FLUSHDB` or `FLUSHALL` command in the source Codis cluster. Otherwise, data may become inconsistent between the Codis cluster and the ApsaraDB for Redis instance.
-   If the data eviction policy \(`maxmemory-policy`\) of the destination database is not set to `noeviction`, data may become inconsistent between the source and destination databases. For more information about data eviction policies, see [How does ApsaraDB for Redis evict data by default?](/intl.en-US/User Guide/FAQ/Expiration policy/How does ApsaraDB for Redis evict data by default?.md)
-   If an expiration policy is enabled for some keys in the source database, some keys may not be deleted in a timely manner after they expired. Therefore, the number of keys in the destination database may be less than that in the source database. You can run the info command to view the number of keys in the destination database.

    **Note:** The number of keys that do not have an expiration policy or have not expired is the same in the source and destination databases.

-   The database version of the destination ApsaraDB for Redis instance must be 2.8, 4.0, or 5.0. The version of the destination database must be the same as or later than the version of the source database. If you synchronize data between different versions of Redis databases, make sure that the versions of the source and destination databases are compatible with each other. You can create a pay-as-you-go ApsaraDB for Redis instance to verify database compatibility. After the verification, you can release the instance or change the billing method to subscription.

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

**Note:**

-   If you run the EVAL or EVALSHA command to call Lua scripts, DTS cannot identify whether these Lua scripts are executed on the destination database. During incremental data synchronization, the destination database does not explicitly return the execution results of Lua scripts.
-   When DTS calls the SYNC or PSYNC command to transfer data of the LIST type, DTS does not clear the existing data. In this case, the destination database may contain duplicate data records.

## Procedure

1.  Purchase a data synchronization instance. For more information, see [Purchase a DTS instance]().

    **Note:** On the buy page, set Source Instance to **Redis**, set Target Instance to **Redis**, and then set Synchronization Topology to **One-Way Synchronization**.

2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

3.  In the left-side navigation pane, click **Data Synchronization**.

4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column.

6.  Configure the source and destination instances.

    ![Configure the source and destination instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0038449951/p71224.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|Select **User-Created Database in ECS Instance**.|
    |Instance Region|The source region that you selected on the buy page. You cannot change the value of this parameter.|
    |ECS Instance ID|Select the ID of the ECS instance that hosts the master node of the Codis-Group. **Note:** DTS synchronizes each Codis-Group of the Codis cluster by using a data synchronization task until the whole cluster is synchronized. In this step, enter the ECS instance ID for the master node of Codis-Group 1. When you configure the data synchronization task for Codis-Group 2, enter the ECS instance ID for the master node of Codis-Group 2. You can configure data synchronization tasks for all Codis-Groups by following the procedure described in this topic. |
    |Database Type|The value of this parameter is set to **Redis**.|
    |Instance Mode|Select **Standalone**. **Note:** You must select **Standalone** for this parameter because data from a Codis cluster cannot be synchronized at a time. DTS synchronizes each Codis-Group of the cluster in a data synchronization task until all Codis-Groups are synchronized. |
    |Port Number|Enter the service port number of the master node in the Codis-Group.|
    |Database Password|The database password of the master node. **Note:** This parameter is optional and can be left blank if no database password is set. |
    |Destination Instance Details|Instance Type|Select **Redis Instance**.|
    |Instance Region|The destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |Instance ID|Select the ID of the destination ApsaraDB for Redis instance.|
    |Database Password|The database password of the destination ApsaraDB for Redis instance. **Note:** The format of the database password is <user\>:<password\>. For example, if the username of a custom account is admin and the password is Rp829dlwa, the database password is admin:Rp829dlwa. |

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the inbound rule of the source ECS instance and the whitelist of the destination ApsaraDB for Redis instance. This ensures that DTS servers can connect to the source and destination instances.

8.  Select the processing mode of conflicting tables, and the objects that you want to synchronize.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4038449951/p71226.png)

    |Setting|Description|
    |:------|:----------|
    |Select the processing mode of conflicting tables|DTS synchronizes each Codis-Group of the Codis cluster in a data synchronization task until the whole cluster is synchronized. When you configure data synchronization for Codis-Group 1, if the ApsaraDB for Redis instance has no data, select **Pre-check and Intercept**. When you configure data synchronization for Codis-Groups 2 to N, select **Ignore**. Otherwise, errors may occur during data synchronization. **Note:**

    -   **Pre-check and Intercept**: checks whether the destination database is empty. If the destination database is empty, the precheck is passed. If the database is not empty, an exception is returned during precheck and the data synchronization task cannot be started.
    -   **Ignore**: skips the precheck for empty destination databases and continues with data synchronization. If the keys in the destination database are the same as those in the source database during data synchronization, the keys in the source database overwrite those in the destination database. |
    |Select the objects to be synchronized|    -   Select one or more databases from the Available section and click ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) to move the databases to the Selected section.
    -   You can select only databases as the objects to be synchronized. You cannot select keys as the objects to be synchronized. |

9.  In the lower-right corner of the page, click **Next**.

10. Configure initial synchronization.

    ![Configure initial synchronization for Redis databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4038449951/p50606.png)

    **Note:** The value is set to **Include full data + incremental data**. DTS synchronizes historical data from the source Codis cluster to the destination Redis database, and then synchronizes incremental data.

11. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, a precheck is performed. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
12. Close the Precheck dialog box after the following message is displayed: **The precheck is passed**.

13. Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    ![Synchronizing](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1038449951/p71258.png)

    **Note:** You can view the status of the data synchronization task on the Synchronization Tasks page.

14. Create and configure a data synchronization task for the other Codis-Group by following Steps [1](#step_sgc_xuk_6os) to 13.


In this topic, the Codis cluster consists of two Codis-Groups. You must create two data synchronization tasks. The following figure shows that the initial synchronization is complete for both tasks and both tasks are in the **Synchronizing** state.

![Synchronization tasks configured for the Codis cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1038449951/p71249.png)

In this topic, databases DB0 and DB1 are synchronized. You can [use Data Management \(DMS\) to log on to the ApsaraDB for Redis instance](~~43847~~) and check the total number of keys in the ApsaraDB for Redis instance. The total number of keys is the same as that in the source Codis cluster.

![](../images/p71281.png "ApsaraDB for Redis instance")

![](../images/p71283.png "Source Codis cluster")

