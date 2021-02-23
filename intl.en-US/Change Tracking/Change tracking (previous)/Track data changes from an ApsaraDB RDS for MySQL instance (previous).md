# Track data changes from an ApsaraDB RDS for MySQL instance \(previous\)

You can use DTS to track data changes in real time. This feature applies to the following scenarios: lightweight cache updates, business decoupling, asynchronous data processing, and real-time synchronization of extract, transform, and load \(ETL\) operations. This topic describes how to track data changes from an ApsaraDB RDS for MySQL instance.

**Note:** This feature is no longer available after March 31, 2020.

## Prerequisites

The database version of the ApsaraDB RDS for MySQL instance is 5.1, 5.5, 5.6, or 5.7.

**Note:** If the source database is ApsaraDB RDS for MySQL 8.0 or a user-created MySQL database, you must use the new change tracking feature. For more information, see [Track data changes from an ApsaraDB RDS for MySQL instance \(new\)](/intl.en-US/Change Tracking/Change tracking (new)/Track data changes from an ApsaraDB RDS for MySQL instance (new).md).

## Precautions

-   A change tracking channel stores the data that was generated in the last 24 hours and deletes expired data.
-   We recommend that you do not use gh-ost or pt-online-schema-change to perform DDL operations on objects when you track data changes. Otherwise, data changes may fail to be tracked.

## Procedure

1.  Create a change tracking instance. For more information, see [Purchase a change tracking instance]().
2.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).
3.  In the left-side navigation pane, click **Change Tracking**.
4.  At the top of the Change Tracking Tasks page, select the region where the change tracking instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3583097951/p51699.png)

5.  Find the change tracking instance and click **Configure Channel** in the Actions column.
6.  Configure the source database for the change tracking task.

    ![Configure the source database](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9886714951/p50973.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|Version|Select **Old**.|
    |Instance Type|Select **RDS Instance**.|
    |Database Type|The value of this parameter is set to **MySQL** and cannot be changed.|
    |Instance Region|The **region** of the source instance. The region is the same as the region that you selected when you purchased the change tracking instance. You cannot change the value of this parameter.|
    |RDS Instance ID|Select the ID of the RDS instance from which you want to track data changes. **Note:** A read-only instance or temporary instance cannot be used as the source instance for change tracking. |
    |Database Account|Enter the database account of the source RDS instance. **Note:**

    -   The account must have the REPLICATION CLIENT permission, the REPLICATION SLAVE permission, the SHOW VIEW permission, and the permission to perform SELECT operations on the required objects.
    -   If the database engine of the source RDS instance is **MySQL 5.5** or **MySQL 5.6**, you do not need to configure the **database account** or **database password**. |
    |Database Password|Enter the password for the database account of the source RDS instance.|

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelist of the source RDS instance. This ensures that DTS servers can connect to the source RDS instance.

8.  In the Create Change Tracking Account message, click **Next** after the account is created.

    **Note:** This step is required only when the database engine of the source RDS instance is **MySQL 5.5** or **MySQL 5.6**. In this step, DTS creates a database account for change tracking in the source RDS instance.

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
    -   If you select a table as the object, DTS tracks only data changes of this table. In this case, if you want to track data changes of another table, you must add the table to the required objects. For more information, see [Modify objects for change tracking](/intl.en-US/Change Tracking/Change tracking (previous)/Modify objects for change tracking.md). |
    |Required Objects|Select objects from the Required Objects section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to add the objects to the Selected section.

**Note:** You can select tables or databases as the objects for change tracking. |

10. In the lower-right corner of the page, click **Save and Precheck**.

    **Note:**

    -   Before you can start the change tracking task, a precheck is performed. You can start the change tracking task only after the task passes the precheck.
    -   If the task fails to pass the precheck, you can click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. You can troubleshoot the issues based on the causes and run a precheck again.
11. Close the Precheck dialog box after the following message is displayed: **The precheck is passed.**

## What to do next

After the change tracking task is configured, DTS performs initial change tracking, which takes about 1 minute. After the initial change tracking is complete, you can consume the tracked data. For more information, see [Use the SDK demo code to consume tracked data](https://www.alibabacloud.com/help/doc-detail/26647.htm).

