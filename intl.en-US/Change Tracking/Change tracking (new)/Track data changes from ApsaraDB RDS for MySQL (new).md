# Track data changes from ApsaraDB RDS for MySQL \(new\)

You can use Data Transmission Service \(DTS\) to track data changes in real time. This feature applies to the following scenarios: lightweight cache updates, business decoupling, and synchronization of extract, transform, and load \(ETL\) operations. This topic describes how to track data changes from ApsaraDB RDS for MySQL.

## Precautions

-   DTS does not track data definition language \(DDL\) operations that are performed by gh-ost or pt-online-schema-change. Therefore, the change tracking client may fail to write the consumed data to the destination tables due to schema conflict.
-   If the source database is used in another task, for example, it is used in a running data migration task, DTS may track data changes of other objects. In this case, you must use the change tracking client to filter the tracked data.

## Procedure

1.  Create a change tracking instance. For more information, see [Purchase a change tracking instance]().
2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).
3.  In the left-side navigation pane, click **Change Tracking**.
4.  At the top of the Change Tracking Tasks page, select the region where the change tracking instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3583097951/p51699.png)

5.  Find the change tracking instance and click **Configure Channel** in the Actions column.
6.  Configure the source database and network type for the change tracking task.

    ![Configure a change tracking task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4483097951/p48078.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |None|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|Instance Type|Select **RDS Instance**. **Note:** If your source database is a user-created database, you must prepare the environment that is required for the source database. For more information, see [Preparation overview](). |
    |Database Type|The value of this parameteris set to **MySQL** and cannot be changed.|
    |Instance Region|The **source region** that you selected on the buy page. You cannot change the value of this parameter.|
    |RDS Instance ID|Select the ID of the RDS instance from which you want to track data changes. **Note:** A read-only instance or temporary instance cannot be used as the source instance for change tracking. |
    |Database Account|Enter the database account of the source RDS instance. **Note:**

    -   The account must have the SELECT permission on the required objects, the REPLICATION CLIENT permission, the REPLICATION SLAVE permission, and the SHOW VIEW permission.
    -   If the database engine of the source RDS instance is **MySQL 5.5** or **MySQL 5.6**, you do not need to configure the **database account** or **database password**. |
    |Database Password|Enter the password for the database account of the source RDS instance.|
    |Network Type|    -   **Classic**
    -   **VPC**
|Select the network type of the change tracking instance.

**Note:**

    -   If your change tracking client is deployed on a local server, you can select **Classic** or **VPC**.
    -   If your change tracking client is deployed on an [ECS](https://www.alibabacloud.com/help/zh/doc-detail/25367.htm) instance, we recommend that you select the network of the ECS instance. For example, if the ECS instance is deployed in a VPC, select VPC as the network type and specify the **VPC** and **vSwitch**.
    -   If you track data changes over internal networks, the network latency is minimal.
    -   **Classic**

If you select **Classic**, no other configurations are required. For more information about the classic network, see [Classic network](https://www.alibabacloud.com/help/zh/doc-detail/61651.htm#h2-url-2).

    -   **VPC**

If you select **VPC**, you must specify the **VPC** and **vSwitch**. For more information about VPC, see [VPC](https://www.alibabacloud.com/help/zh/doc-detail/61651.htm#h2-url-1). |

7.  Click **Set Whitelist and Next**. The following table describes the details about whitelist settings.

    |Instance type of the source database|Description|
    |------------------------------------|-----------|
    |RDS MySQL|The CIDR blocks of DTS servers are automatically added to the whitelist of the source ApsaraDB RDS for MySQL instance. This ensures that DTS servers can connect to the source RDS instance.|
    |User-created MySQL database hosted on ECS|The CIDR blocks of DTS servers are automatically added to an inbound rule of the ECS instance. This ensures that DTS servers can connect to the source instance.|
    |    -   User-created MySQL database with a public IP address
    -   User-created MySQL database connected over Express Connect, VPN Gateway, or Smart Access Gateway
|If a whitelist is configured for the user-created database, you must add the CIDR blocks of DTS servers to the whitelist of the database. For more information, see [Add the CIDR blocks of DTS servers to the security settings of on-premises databases]().|

8.  In the Create Change Tracking Account message, click **Next** after the account is created.

    **Note:** This step is required only when the database engine of the source RDS instance is **MySQL 5.5** or **MySQL 5.6**. In this step, DTS creates a database account for change tracking in the source instance.

9.  Select the data change types and objects.

    ![Select the data change types and objects](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0308539951/p48087.png)

    |Parameter|Description|
    |:--------|:----------|
    |Required Data Types|    -   **Data Updates**

DTS tracks data updates of the selected objects, including the INSERT, DELETE, and UPDATE operations.

    -   **Schema Updates**

DTS tracks the create, delete, and modify operations that are performed on all object schemas of the source instance. You must use the change tracking client to filter the required data.

**Note:**

    -   If you select a database as the object, DTS tracks data changes of all objects, including new objects in the database.
    -   If you select a table as the object, DTS tracks only data changes of this table. In this case, if you want to track data changes of another table, you must add the table to the required objects. For more information, see [Modify objects for change tracking](/intl.en-US/Change Tracking/Change tracking (new)/Modify objects for change tracking.md). |
    |Required Objects|Select objects from the Required Objects section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to add the objects to the Selected section.

**Note:** You can select tables and databases as the objects for change tracking. |

10. In the lower-right corner of the page, click **Save and Precheck**.

    **Note:**

    -   Before you can start the change tracking task, a precheck is performed. You can start the change tracking task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run a precheck again.
11. Close the Precheck dialog box after the following message is displayed: **The precheck is passed.**

After the change tracking task is configured, DTS performs initial change tracking, which takes about 1 minute. After the initial change tracking is complete, you can create one or more consumer groups to consume the tracked data.

## What to do next

1.  [Create a consumer group](/intl.en-US/Change Tracking/Change tracking (new)/Create a consumer group.md)
2.  [Use a Kafka client to consume tracked data](/intl.en-US/Change Tracking/Change tracking (new)/Use a Kafka client to consume tracked data.md)

