# Remove an object from a data synchronization task

When a data synchronization task is running, you can add objects to the task or remove objects from the task. You do not need to configure the task again. This topic describes how to remove an object from a data synchronization task.

## Prerequisites

-   The data synchronization task is in the **Synchronizing**, **Paused**, or **Synchronization Failed** state.
-   The source and destination databases are not in the process of upgrade, configuration change, network switchover, or cross-zone migration. This ensures that DTS can connect to the source and destination databases. This also ensures that DTS can read database and table information from the source database.

## Precautions

After an object is removed from a data synchronization task, the task no longer synchronizes incremental data of the object to the destination database.

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).
2.  In the left-side navigation pane, click **Data Synchronization**.
3.  At the top of the Synchronization Tasks page, select the region where the data synchronization instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

4.  Find the data synchronization task and choose **More** \> **Modify Objects to Synchronize** in the **Actions** column.

    ![Reselect the objects to be synchronized](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8600398951/p49162.png)

5.  In the Selected section, click the object that you want to remove, and click the ![Left arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2308539951/p48160.png) icon to move the object to the Available section.

    ![Remove an object from a data synchronization task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6208539951/p49188.png)

6.  Click **Precheck**.

    **Note:**

    -   After you reselect the objects to be synchronized, DTS performs a precheck on the data synchronization task. The data synchronization task can be started only after it passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run a precheck again.
7.  Start the data synchronization task.

