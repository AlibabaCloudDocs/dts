# Synchronize data from a user-created PostgreSQL database to an AnalyticDB for PostgreSQL instance

This topic describes how to synchronize data from a user-created PostgreSQL database to an AnalyticDB for PostgreSQL instance by using Data Transmission Service \(DTS\). The data synchronization feature provided by DTS allows you to transfer and analyze data with ease.

-   The tables to be synchronized from the user-created PostgreSQL database contain primary keys.
-   An AnalyticDB for PostgreSQL instance is created. For more information, see [Create an AnalyticDB PostgreSQL instance](https://www.alibabacloud.com/help/zh/doc-detail/50200.htm).

## Precautions

-   A single data synchronization task can synchronize data from only one database. To synchronize data from multiple databases, you must create a data synchronization task for each database.
-   During data synchronization, new tables that are created in the source database can also be synchronized. However, to ensure data consistency, you must run the following statement on the new tables before they can be synchronized:

    ```
    ALTER TABLE schema.table REPLICA IDENTITY FULL;
    ```


## Limits

-   Initial schema synchronization is not supported. DTS does not synchronize the schemas of the required objects from the source database to the destination database.
-   You can select only tables as the objects to be synchronized.
-   You cannot synchronize the following types of data: BIT, VARBIT, GEOMETRY, ARRAY, UUID, TSQUERY, TSVECTOR, and TXID\_SNAPSHOT.
-   If you perform a DDL operation on an object to be synchronized in the source database during data synchronization, you must perform the operation in the destination database. Then, you must restart the data synchronization task.

## SQL operations that can be synchronized

INSERT, UPDATE, and DELETE

## Before you begin

Create a database, schema, and table in the destination AnalyticDB for PostgreSQL instance based on the schema of the objects to be synchronized. For more information, see [SQL syntax](https://www.alibabacloud.com/help/zh/doc-detail/118877.htm).

## Procedure

1.  Purchase a data synchronization instance. For more information, see [Purchase procedure]().

    **Note:** On the buy page, set Source Instance to **PostgreSQL**, set Target Instance to **AnalyticDB for PostgreSQL**, and set Synchronization Topology to **One-Way Synchronization**.

2.  Configure the source and destination databases.

    ![Configure the source and destination databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5610398951/p83405.png)

    |Section|Parameter|Description|
    |-------|---------|-----------|
    |N/A|Synchronization Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Instance Details|Instance Type|Select an instance type based on the deployment of the source database. In this example, select **User-Created Database in ECS Instance**. **Note:** If you select other instance types, you must prepare the environment that is required for the source database. For more information, see [Preparation overview]().

This topic uses **User-Created Database in ECS Instance** as an example to describe how to configure a data synchronization task. |
    |Instance Region|The source region that you selected on the buy page. You cannot change the value of this parameter.|
    |ECS Instance ID|Select the ID of the ECS instance on which the user-created PostgreSQL database is hosted.|
    |Database Type|The value of this parameter is set to **PostgreSQL** and cannot be changed.|
    |Port Number|Enter the service port number of the user-created PostgreSQL database.|
    |Database Name|Enter the name of the source database.|
    |Database Account|Enter the account of the user-created PostgreSQL database. **Note:** The account must have the owner permission on schemas. |
    |Database Password|Enter the password of the source database account.|
    |Destination Instance Details|Instance Type|The value of this parameter is set to **AnalyticDB for PostgreSQL** and cannot be changed.|
    |Instance Region|The region of the destination instance. The region is the same as the destination region that you selected on the buy page. You cannot change the value of this parameter.|
    |Instance ID|Select the ID of the AnalyticDB for PostgreSQL.|
    |Database Name|Enter the name of the destination database.|
    |Database Account|Enter the **initial account** of the AnalyticDB for PostgreSQL instance. For more information, see [t16843.md\#](/intl.en-US/Quick Start/Configure an account.md). **Note:** You can also enter an account that has the RDS\_SUPERUSER permission. For more information, see [Manage users and permissions](/intl.en-US/Beginner Developer Guide/Manage users and permissions.md). |
    |Database Password|Enter the password of the destination database account.|

3.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the inbound security group rule of the ECS instance and the whitelist of the AnalyticDB for PostgreSQL instance. This ensures that DTS servers can connect to the source and destination instances.

4.  Select the synchronization policy and the objects to be synchronized.

    ![Select the objects to be synchronized](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5610398951/p99535.png)

    |Setting|Parameter|Description|
    |:------|:--------|:----------|
    |Select the synchronization policy|Initial Synchronization|**Initial Full Data Synchronization** is selected by default. After the precheck, DTS synchronizes historical data of the required objects from the source instance to the destination instance. The data is the basis for subsequent incremental synchronization.|
    |Processing Mode In Existed Target Table|    -   **Clear Target Table**

Skips the **Schema Name Conflict** item during the precheck. Clears the data in the destination table before initial full data synchronization. If you want to synchronize your business data after testing the data synchronization task, you can select this mode.

    -   **Ignore**

Skips the **Schema Name Conflict** item during the precheck. Adds data to the existing data during initial full data synchronization. You can select this mode if you want to synchronize data from multiple tables to one table. |
    |Synchronization Type|Select the types of operations that you want to synchronize based on your business requirements.

**Note:** The **Alter Table** operation is not supported.

    -   **Insert**
    -   **Update**
    -   **Delete**
    -   **AlterTable** |
    |Select the objects to be synchronized|N/A|Select tables from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the tables to the Selected section.

**Note:**

    -   You can select only tables as the objects to be synchronized.
    -   You can use the object name mapping feature to change the names of the columns that are synchronized to the destination database. For more information, see [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md). |

5.  In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data synchronization task, a precheck is performed. You can start the data synchronization task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
6.  Close the Precheck dialog box after the following message is displayed: **The precheck is passed**.

7.  Wait until the initial synchronization is complete and the data synchronization task is in the **Synchronizing** state.

    On the Synchronization Tasks page, view the status of the data synchronization task.

    ![View the status of a data synchronization task.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5130359951/p41059.png)


