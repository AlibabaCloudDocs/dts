# Synchronize data between ApsaraDB for Redis cluster instances across Alibaba Cloud accounts

Data Transmission Service \(DTS\) allows you to perform one-way synchronization between ApsaraDB for Redis cluster instances that belong to different Alibaba Cloud accounts. This feature is applicable to scenarios such as resource migration or resource merging across Alibaba Cloud accounts and business architecture adjustment.

-   If the source Redis instance is a Community Edition instance, the engine version must be 4.0 or 5.0. If the source Redis instance is an Enhanced Edition instance, the engine version must be 5.0.

    **Note:** The engine version of the destination Redis instance can be 4.0 or 5.0. The engine version of the destination Redis instance cannot be earlier than the engine version of the source Redis instance. Before you synchronize data between two Redis instances that use different engine versions, check the compatibility of the two versions. For example, you can create a destination Redis instance based on the pay-as-you-go billing method to verify the compatibility between the source and destination instances. Then, you can release this instance or change the billing method of the instance to subscription.

-   The source Redis instance is deployed in a virtual private cloud \(VPC\). If the source instance is deployed in the classic network, you can change the network type to VPC. For more information, see [Switch to VPC network](/intl.en-US/User Guide/Manage instances/Network connection management/Switch to VPC network.md).
-   Secure Sockets Layer \(SSL\) encryption is disabled for the source Redis instance. For more information, see [Configure SSL encryption](/intl.en-US/User Guide/Security management/Configure SSL encryption.md).
-   The available storage space of the destination Redis instance is larger than the used storage space of the source Redis instance.
-   ApsaraDB for Redis Enhanced Edition is integrated with more Redis modules than ApsaraDB for Redis Community Edition. For more information, see [Integration of multiple Redis modules](). To ensure compatibility between the source and destination instances, the edition of the destination instance must be Enhanced if the edition of the source instance is Enhanced.

Two Redis instances are created under different Alibaba Cloud accounts. Assume that you need to migrate data from the instance of Account A to the instance of Account B. The following figure shows the architecture of the migration solution.

![Data migration between Redis instances of different accounts](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2950001061/p132220.png)

**Note:** To synchronize data between Redis instances of different accounts, note that the source and destination instances must be deployed in the cluster or standard architecture. In this scenario, the read/write splitting architecture is not supported.

DTS provides the following data migration solution:

DTS cannot migrate data of an ApsaraDB for Redis cluster instance. In this solution, the data synchronization feature of DTS is used to synchronize data to the destination instance. The following table describes how to configure a data synchronization task.

|Step|Description|
|----|-----------|
|1. Use Account A to log on to the Alibaba Cloud Management Console and grant the required permission to a RAM role. For more information, see Step 1 in [Preparations](#section_b4m_p6z_fi6).|When you configure the RAM role, set Account B as the trusted account, and authorize the RAM role to access the resources of Account A.|
|2. Use Account A to log on to the Alibaba Cloud Management Console and prepare the environment that is required for the source Redis instance. For more information, see Steps 2 to 4 in [Preparations](#section_b4m_p6z_fi6).|Add the CIDR blocks of DTS servers to the whitelist of the source Redis instance. Apply for a private endpoint for the source Redis instance, and create a database account that is authorized to **replicate** the data of the source Redis instance.**Note:** You are not allowed to create a database account that is authorized to **replicate** the data of ApsaraDB for Redis cluster instances. To enable this feature, [submit a ticket](https://workorder-intl.console.aliyun.com/?#/ticket/add/?productId=1226). |
|3. Use Account B to log on to the Alibaba Cloud Management Console and configure the data synchronization task. For more information, see [Procedure](#section_hck_cyj_2o4).|DTS cannot read the information of source Redis instance across Alibaba Cloud accounts. When you configure the data synchronization task, you must specify the source instance as a user-created database that is connected to DTS over Express Connect.|

## Precautions

-   DTS uses the resources of the source and destination instances during initial full data synchronization. This may increase the loads of the database servers. If you synchronize a large volume of data or the server specifications cannot meet your requirements, the database services may become unavailable. Before you synchronize data, evaluate the impact of data synchronization on the performance of the source and destination instances. We recommend that you synchronize data during off-peak hours.
-   We recommend that you do not run the `FLUSHDB` or `FLUSHALL` command on the source instance during data synchronization. If you run one of the two commands, data may become inconsistent between the source and destination instances.
-   If the data eviction policy \(`maxmemory-policy`\) of the destination database is not set to `noeviction`, data may become inconsistent between the source and destination databases. For more information about data eviction policies, see [How does ApsaraDB for Redis evict data by default?](/intl.en-US/User Guide/FAQ/Expiration policy/How does ApsaraDB for Redis evict data by default?.md)
-   If an expiration policy is enabled for some keys in the source database, some keys may not be deleted in a timely manner after they expired. Therefore, the number of keys in the destination database may be less than that in the source database. You can run the info command to view the number of keys in the destination database.

    **Note:** The number of keys that do not have an expiration policy or have not expired is the same in the source and destination databases.


## Operations that can be synchronized

|Edition|Operation|
|-------|---------|
|ApsaraDB for Redis Community Edition|-   APPEND
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
-   SWAPDB and UNLINK \(supported only if the engine version of the source Redis instance is 4.0\) |
|ApsaraDB for Redis Enhanced Edition \(Tair\)|-   APPEND
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
-   UNLINK, ZADD, ZINCRBY, ZINTERSTORE, ZREM, ZREMRANGEBYLEX, ZUNIONSTORE, ZREMRANGEBYRANK, and ZREMRANGEBYSCORE |

**Note:**

-   If you run the EVAL or EVALSHA command to call Lua scripts, DTS cannot identify whether these Lua scripts are executed on the destination database. During incremental data synchronization, the destination database does not explicitly return the execution results of Lua scripts.
-   When DTS calls the SYNC or PSYNC command to transfer data of the LIST type, DTS does not clear the existing data. In this case, the destination database may contain duplicate data records.

## Preparations

Use the Alibaba Cloud account that owns the source Redis instance to log on to the [Alibaba Cloud Management Console](https://homenew.console.aliyun.com/). Then, perform the following steps:

1.  Create a RAM role and authorize the RAM role to access the resources of the Alibaba Cloud account. For more information, see [Configure RAM authorization for data migration or synchronization from a user-created database in a VPC across different Alibaba Cloud accounts](/intl.en-US/RAM-based Access Control/Configure RAM authorization for data migration or synchronization from a user-created
         database in a VPC across different Alibaba Cloud accounts.md).

2.  Apply for a private endpoint for the source Redis instance. For more information, see [Enable a direct connection](/intl.en-US/User Guide/Manage instances/Network connection management/Enable a direct connection.md).

3.  Select the CIDR blocks of the DTS servers that are deployed in the region of the source Redis instance. Add these CIDR blocks to the whitelist of the source Redis instance. For more information, see [Step 2: Set IP address whitelists](/intl.en-US/Quick Start/Step 2: Set IP address whitelists.md).

    **Note:** For more information about the CIDR blocks of the DTS servers in each region, see [Add the CIDR blocks of DTS servers to the security settings of on-premises databases]().

4.  Create a database account for the source Redis instance.

    1.  Submit a ticket to create a database account that is authorized to replicate the data of your ApsaraDB for Redis cluster instance.

        **Note:** By default, you are not allowed to create a database account that is authorized to **replicate** the data of ApsaraDB for Redis cluster instances. To enable this feature, [submit a ticket](https://workorder-intl.console.aliyun.com/?#/ticket/add/?productId=1226).

    2.  Create a database account that is authorized to **replicate** data of the source instance. For more information, see [Create and manage database accounts](/intl.en-US/User Guide/Security management/Create and manage database accounts.md).


## Procedure

1.  Use the Alibaba Cloud account that owns the destination Redis instance to log on to the [Alibaba Cloud Management Console](https://homenew.console.aliyun.com/). Then, purchase a data synchronization instance. For more information, see [Purchase a DTS instance]().

    **Note:** On the buy page, set the following parameters:

    -   Set Source Instance to **Redis**, set Target Instance to **Redis**, and set Synchronization Topology to **One-Way Synchronization**.
    -   Set the Source Region parameter based on the region where the source Redis instance resides, and set the Target Region parameter based on the region where the destination Redis instance resides.
2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

3.  In the left-side navigation pane, click **Data Synchronization**.

4.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

5.  Find the data synchronization instance and click **Configure Synchronization Channel** in the Actions column.

6.  Configure the source and destination instances.

    1.  Specify an informative task name for easy identification. You do not need to specify a unique task name.

    2.  Select **User-Created Database Connected over Express Connect, VPN Gateway, or Smart Access Gateway** as the instance type. Then, click **VPC of Another Alibaba Cloud Account** next to the Peer VPC field.

        **Note:** DTS cannot read the information of source Redis instance across Alibaba Cloud accounts. When you configure the data synchronization task, you must specify the source instance as a user-created database that is connected to DTS over Express Connect.

        ![Select a VPC under another Alibaba Cloud account](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0919207951/p132244.gif)

    3.  Configure the source instance.

        ![Configure the source instance](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0919207951/p132253.png)

        |Parameter|Description|
        |:--------|:----------|
        |Instance Type|Select **User-Created Database Connected over Express Connect, VPN Gateway, or Smart Access Gateway**.|
        |Instance Region|The source region that you selected on the buy page. You cannot change the value of this parameter.|
        |Alibaba Cloud Account ID|Enter the ID of the Alibaba Cloud account that owns the source Redis instance. **Note:** To obtain the ID of the Alibaba Cloud account that owns the source Redis instance, you must log on to the [Account Management](https://account.console.aliyun.com/#/secure) console by using this account. The account ID is displayed on the Security Settings page.

![Obtain an Alibaba Cloud account ID](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4634948951/p44838.png) |
        |Role Name|Enter the name of the RAM role that you created in Step 1 of [Preparations](#section_b4m_p6z_fi6).|
        |Peer VPC|Select the VPC where the source Redis instance is deployed.|
        |Database Type|Select **Redis**.|
        |Instance Mode|Select **Cluster**.|
        |IP Address|Enter the IP address that corresponds to the private endpoint that you obtained for the source Redis instance in Step 2 of [Preparations](#section_b4m_p6z_fi6). In this example, enter 192.168.0.153. **Note:** To obtain the IP address that corresponds to the private endpoint of your source Redis instance, run the ping command, for example, `ping r-********.redis.rds.aliyuncs.com`. |
        |Port Number|Enter the service port number of the source Redis instance.|
        |Database Password|Enter the password of the account that you created in Step 4 of [Preparations](#section_b4m_p6z_fi6). This account is authorized to **replicate** the data of the source Redis instance.**Note:** The format of the database password is <user\>:<password\>. For example, if the username of a custom account is admin and the password is Rp829dlwa, the database password is admin:Rp829dlwa. |

    4.  Configure the destination instance.

        ![Configure the destination instance](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0919207951/p132254.png)

        |Parameter|Description|
        |:--------|:----------|
        |Instance Type|Select **Redis Instance**.|
        |Instance Region|The destination region that you selected on the buy page. You cannot change the value of this parameter.|
        |Instance ID|Select the ID of the destination ApsaraDB for Redis instance.|
        |Database Password|Enter the database password of the destination Redis instance. The account must have the **read and write** permissions on the destination database.**Note:** The format of the database password is <user\>:<password\>. For example, if the username of a custom account is admin and the password is Rp829dlwa, the database password is admin:Rp829dlwa. |

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** In this step, DTS adds the CIDR blocks of DTS servers to the whitelist of the destination Redis instance. This ensures that the DTS servers can connect to the source and destination Redis instances.

8.  Select the processing mode of conflicting tables, and the objects that you want to synchronize.

    ![Select the objects to be synchronized](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1455721061/p71478.png)

    |Setting|Description|
    |:------|:----------|
    |Select the processing mode of conflicting tables|    -   **Pre-check and Intercept**: checks whether the destination database is empty. If the destination database is empty, the precheck is passed. If the database is not empty, an error is returned during the precheck and the data synchronization task cannot be started.
    -   **Ignore**: skips the check for empty destination database.

**Warning:** If you select **Ignore**, the data records in the source database overwrite the data records that have the same keys in the destination database. |
    |Select the objects to be synchronized|    -   Select one or more databases from the Available section and click the ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the databases to the Selected section.
    -   You can select only databases as the objects to be synchronized. You cannot select keys as the objects to be synchronized. |

9.  In the lower-right corner of the page, click **Next**.

10. Configure initial synchronization.

    ![Configure initial synchronization](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4038449951/p50606.png)

    **Note:**

    -   The value is set to **Include full data + incremental data**. DTS synchronizes historical data of the source Redis instance to the destination Redis instance and then synchronizes incremental data.
    -   If a version-related error message appears, upgrade the source Redis instance to a specified version. For more information, see [Upgrade the major version](/intl.en-US/User Guide/Manage instances/Lifecycle management/Upgrade the major version.md) and [Upgrade the minor version](/intl.en-US/User Guide/Manage instances/Lifecycle management/Upgrade the minor version.md).
11. In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, a precheck is performed. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
12. Close the Precheck dialog box after the following message is displayed: **The precheck is passed**.

13. Wait until the initial synchronization is complete and the data synchronization task enters the **Synchronizing** state.

    ![Status of the data synchronization task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5130359951/p41059.png)

    **Note:** You can view the status of the data synchronization task on the **Synchronization Tasks** page.


