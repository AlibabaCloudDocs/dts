# Modify the objects for change tracking

Data Transmission Service \(DTS\) allows you to add or remove the objects for change tracking in the consumption process. This topic describes how to modify the objects for change tracking.

## Precautions

-   After you add an object, the change tracking task pulls the data changes of the new object from the time when the modification takes effect.
-   If the change tracking client tracks the data changes of a removed object, you must filter the tracked data changes on the client.

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).
2.  In the left-side navigation pane, click **Change Tracking**.
3.  At the top of the Change Tracking Tasks page, select the region where the change tracking instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3583097951/p51699.png)

4.  Find the change tracking instance and click **Modify Required Objects** in the **Actions** column.
5.  In the Select Required Objects step, you can add or remove the objects for change tracking.

    ![Modify the objects for change tracking](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0308539951/p48087.png)

    -   Add the objects for change tracking

        In the Required Objects section, select one or more objects and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to add the objects to the Selected section.

    -   Remove the objects for change tracking

        In the Selected section, select one or more objects and click the ![Left arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2308539951/p48160.png) icon to move the objects to the Required Objects section.

6.  In the lower-right corner of the page, click **Save and Precheck**.

    **Note:**

    -   Before you can start the change tracking task, a precheck is performed. You can start the change tracking task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run a precheck again.
7.  Close the Precheck dialog box after the following message is displayed: **The precheck is passed.**

After the change tracking task is configured, DTS performs initial change tracking, which takes about 1 minute. After the initial change tracking is complete, you can use the tracked data changes for subsequent operations. For more information, see [Use a Kafka client to consume tracked data](/intl.en-US/Change Tracking/Change tracking (new)/Use a Kafka client to consume tracked data.md).

