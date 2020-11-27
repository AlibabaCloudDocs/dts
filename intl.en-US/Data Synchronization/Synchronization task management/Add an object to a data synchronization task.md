# Add an object to a data synchronization task

When a data synchronization task is running, you can add objects to the task or remove objects from the task. You do not need to configure the task again. This topic describes how to add an object to a data synchronization task.

## Prerequisites

-   The data synchronization task is in the **Synchronizing**, **Paused**, or **Synchronization Failed** state.
-   The source and destination databases are not in the process of upgrade, configuration change, network switchover, or cross-zone migration. This ensures that DTS can connect to the source and destination databases. This also ensures that DTS can read database and table information from the source database.

## Precautions

The time when DTS synchronizes data of a new object depends on whether **initial synchronization** is configured for the data synchronization task.

-   If initial synchronization is not configured, DTS synchronizes data after incremental data is generated on the source instance.
-   If initial synchronization is configured, DTS synchronizes schemas and historical data, and then synchronizes incremental data.

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).
2.  In the left-side navigation pane, click **Data Synchronization**.
3.  At the top of the Synchronization Tasks page, select the region where the data synchronization instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

4.  Find the data synchronization task and choose **More** \> **Modify Objects to Synchronize** in the **Actions** column.

    ![Reselect the objects to be synchronized](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8600398951/p49162.png)

5.  In the Available section, click the object that you want to add, and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the object to the Selected section.

    ![Add an object to a data synchronization task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6208539951/p49163.png)

6.  Click **Precheck**.

    **Note:**

    -   After you reselect the objects to be synchronized, DTS performs a precheck on the data synchronization task. The data synchronization task can be started only after it passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run a precheck again.
7.  Start the data synchronization task.

