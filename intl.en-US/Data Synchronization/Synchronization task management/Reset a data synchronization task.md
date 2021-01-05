# Reset a data synchronization task

This topic describes how to reset data synchronization task. You can reset a data synchronization task to stop data synchronization or reconfigure data synchronization.

The data synchronization task is not in the **Not Configured** status.

## Impacts on billing

-   Subscription: no impact.
-   Pay-as-you-go: The data synchronization task will enter the **Not Configured** state. You are not billed for the task when it is in this state. The billing restarts only after you configure and start the data synchronization task.

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Data Synchronization**.

3.  At the top of the Synchronization Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4130359951/p50604.png)

4.  Find the target synchronization task, and choose **More** \> **Reset Task** in the **Actions** column.

    **Warning:** Resetting a data synchronization task has the following impacts. Proceed with caution.

    -   The data synchronization task stops Data Synchronization. Incremental data changes and schema changes in the source databases will not be synchronized to the destination databases.
    -   The configuration of data synchronization task will be cleared and changed to **Not Configured** status.
5.  In the dialog box that appears, click **OK**.


## References

For information about how to configure a data synchronization task, see [Overview of data synchronization scenarios](/intl.en-US/Data Synchronization/Overview of data synchronization scenarios.md).

