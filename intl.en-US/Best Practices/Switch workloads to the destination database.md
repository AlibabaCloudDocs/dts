# Switch workloads to the destination database

This topic describes how to switch your workloads to the destination database and prepare a rollback solution. This allows you to minimize the negative impact of data migration on your business.

A data migration task is configured and it is in the **Migrating** or **Completed** state. For more information, see [Overview of data migration scenarios](/intl.en-US/Data Migration/Overview of data migration scenarios.md).

## Precautions

-   We recommend that you switch workloads to the destination database during off-peak hours to minimize the negative impact. Before you switch workloads to the destination database, you must stop writing data to the source database and suspend the business.
-   We recommend that you create and authorize a database account for data migration. This allows you to distinguish session information and improve data security.

## Procedure

1.  Wait until the task progress bar shows **Incremental Data Migration** and **The migration task is not delayed** or a delay time of less than 5 seconds.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1940359951/p72817.png)

    **Note:** If you do not select **Incremental Data Migration** when you configure the data migration task, the task progress bar does not show **Incremental Data Migration**. After data is migrated, the migration task automatically ends. In this case, you must suspend the business and stop writing data to the source database before you run the data migration task. Skip to Step [5](#step_ft2_c3e_up7) and proceed.

2.  Suspend the business and stop writing data to the source database.

3.  Log on to the source database and run the following statements based on the database type to view the session information. Make sure that no new sessions are used for write operations.

    **Note:** You can view the processes or sessions between DTS and the source database by running the preceding statements.

4.  After the status of **incremental data migration** changes to **The migration task is not delayed** again, wait for one minute or longer, and then manually stop the migration task.

    ![The migration task is not delayed](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2940359951/p47604.png)

5.  During service interruption, remove the limit on writing data to the source database.

6.  Create and start a task to migrate incremental data generated in the destination database to the source database. The migration task created in this step provides a rollback solution. If an error occurs in the destination database, you can switch workloads to the source database.

    For example, data is migrated from a self-managed MySQL database to an ApsaraDB RDS for MySQL instance. To create a task in the opposite direction, see [Migrate data from an ApsaraDB RDS for MySQL database to a user-created MySQL database](/intl.en-US/Data Migration/Migration from Alibaba Cloud to a user-created database/Migrate data from an ApsaraDB RDS for MySQL database to a user-created MySQL database.md) \(select only **Incremental Data Migration**\).

    **Warning:** When you configure a data migration task in the opposite direction, you must select only **Incremental Data Migration** in the **Configure Migration Types and Objects** step. Then, you must select the database or table to be migrated back to the source database.

    ![Select only Incremental Data Migration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0524948951/p72718.png)

7.  Verify that the data of the source and destination databases is consistent, switch workloads to the destination database, and then resume your business.

8.  After you run the task in the opposite direction, incremental data generated in the destination database is migrated back to the source database in real time. If the business fails, you can switch workloads back to the source database.


## What to do next

After you switch workloads to the destination database and test all the business-related features, you can stop the task in the opposite direction. For more information, see [Stop a data migration task](/intl.en-US/Data Migration/Migration task management/Stop a data migration task.md).

**Warning:** The database accounts that are used for data migration have the read and write permissions. After data is migrated, you must delete the accounts or revoke the write permission to ensure security.

## FAQ

-   Q: What can I do if an error occurs after I switch workloads to the destination database?

    A: If an error occurs, you can switch workloads back to the source database. After you run the task in the opposite direction, incremental data generated in the destination database is migrated back to the source database in real time.

-   Q: How can I ensure data consistency in the source database if I am unable to switch workloads to the destination database?

    A: You can back up the source database before you switch workloads.

-   Q: What can I do if data is written to the source database due to a misoperation after I switch workloads to the destination database?

    A: You can compare data of the source and destination database through data verification and manually change data to ensure consistency.


