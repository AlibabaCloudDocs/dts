# Set alert rules for DTS tasks in the Cloud Monitor console

This topic describes how to set alert rules for Data Transmission Service \(DTS\) tasks in the Cloud Monitor console. You can set threshold-based alerts and event-based alerts to help detect abnormal data and troubleshoot errors in a timely manner.

Cloud Monitor allows you to monitor DTS tasks based on latency and task status.

|Metric|Description|Alert type|
|------|-----------|----------|
|Latency|Monitors the latency of DTS tasks. If the latency of a task exceeds the specified threshold, an alert is triggered. Unit: milliseconds.|[Threshold-based alert](#section_xhq_9do_o6t)|
|Task status|Monitors the status change of DTS tasks. If the status of a task changes to **Error** or **Restore**, an alert is triggered.|[Event-based alert](#section_b42_etp_505)|

## Set a threshold-based alert

1.  Log on to the [云监控控制台](https://cloudmonitor.console.aliyun.com)[Cloud Monitor console](https://cms-intl.console.aliyun.com)[Cloud Monitor console](https://partners-intl.console.aliyun.com/#/cms).

2.  In the left-side navigation pane, choose **Alerts** \> **Alert Rules**.

3.  On the **Threshold Value Alert** tab, click **Create Alert Rule**.

4.  Set parameters for the alert rule.

    1.  Set the related resource.

        ![Set the related resource](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8953541061/p132522.png)

        |Parameter|Description|
        |---------|-----------|
        |**Product**|Select a DTS instance type from the drop-down list:        -   **DTS\(Migration\)**: data migration instance
        -   **DTS\(Change\_tracking\)**: change tracking instance
        -   **DTS\(Synchronization\)**: data synchronization instance
**Note:** You can enter dts in the search box to find a DTS instance type. |
        |**Resource Range**|Select one of the following options based on your business requirements:        -   **All Resources**: The alert rule is applied to all your instances of the specified type.

**Note:** An alert rule can be applied to a maximum of 1,000 instances. If you have more than 1,000 instances, you may not receive notifications when the alert threshold is reached. We recommend that you add resources to service-specific application groups before you create an alert rule.

        -   **Instance**: The alert rule is applied to one or more specific instances. If you select this option, you must set the Instance parameter. |
        |**Instance**|Select one or more instances from the drop-down list.|

    2.  Configure the alert rule.

        ![Configure an alert rule](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8953541061/p132535.png)

        |Parameter|Description|
        |---------|-----------|
        |**Alert Rule**|Enter the name of the alert rule.|
        |**Rule Description**|Specify the details of the alert rule. This parameter defines the conditions to trigger alerts.In this example, Cloud Monitor checks the **migration latency**. An alert is triggered if the average latency is 20 seconds or more for three consecutive periods of 5 minutes. |
        |**Mute for**|Set a mute period. If the alert is not cleared within the mute period, a new alert notification is sent when the mute period ends.|
        |**Effective Period**|Set the period during which the alert rule is effective. The system monitors the metrics and generates alerts only during the effective period.|

    3.  Set the notification method.

        ![设置通知方式](../images/p132552.png)

        ![Set the notification method](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8953541061/p132554.png)

        |Parameter|Description|
        |---------|-----------|
        |**Notification Contact**|Select a contact group in the **Contact Group** section, and then click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8953541061/p132551.png) icon to move the contact group to the **Selected Groups** section.**Note:** Before you set an alert rule, you must create an alert contact or contact group in the Cloud Monitor console. For more information, see [报警人和报警联系组](https://help.aliyun.com/document_detail/28609.html)[Create an alert contact or alert group](https://www.alibabacloud.com/help/zh/doc-detail/28609.htm). |
        |**Notification Methods**|This parameter is set to **Email + DingTalk \(Info\)** and cannot be changed.

根据业务需求，选择：

        -   **电话+短信+邮件+钉钉机器人**
        -   **短信+邮件+钉钉机器人**
        -   **邮件+钉钉机器人** |
        |**Auto Scaling**|If you select **Auto Scaling**, you must specify the scaling rule that is triggered when an alert is sent.|
        |**Log Service**|If you select **Log Service**, you must specify the Log Service project, the region of the project, and the Logstore that stores alert messages.|
        |**Email Subject**|If you set **Resource Range** to **Instance**, you can specify the subject of the alert notification email. The default email subject is Product name + Metric name + Instance ID.|
        |**Email Remark**|Enter the additional information that you want to include in the alert notification email.|
        |**HTTP CallBack**|Enter a callback URL that is accessible over the Internet. Cloud Monitor pushes an alert notification to the specified callback URL by sending an HTTP Post request. Only the HTTP protocol is supported. For more information, see [使用报警回调](https://help.aliyun.com/document_detail/60714.html)[Use alert callbacks](https://www.alibabacloud.com/help/zh/doc-detail/60714.htm).|

    4.  Click **Confirm**.


## Set an event-based alert

1.  Log on to the [云监控控制台](https://cloudmonitor.console.aliyun.com)[Cloud Monitor console](https://cms-intl.console.aliyun.com)[Cloud Monitor console](https://partners-intl.console.aliyun.com/#/cms).

2.  In the left-side navigation pane, choose **Alerts** \> **Alert Rules**.

3.  Click the **Event Alert** tab. On this tab, click **Create Event Alert**.

4.  Set parameters for the alert rule.

    |Parameter|Description|
    |---------|-----------|
    |**Alert Rule Name**|Enter the name of the alert rule.|
    |**Event Type**|Select **System Event**.|
    |**Product Type**|Select **Data Transmission**.|
    |**Event Type**|Keep the default value **All types**.|
    |**Event Level**|Keep the default value **All Levels**.|
    |**Event Name**|Select an event name from the drop-down list. If a DTS task \(data migration task, data synchronization task, or change tracking task\) is resumed or encounters an error, an alert is triggered.|
    |**Resource Range**|Select **All Resources** or **Application Groups**. If you select **Application Groups**, you must specify the group information.|
    |**Alert Notification**|Set the alert notification method. For more information about the parameters, see [Step 4](#step_ej5_39e_d9a).|

5.  Click **OK**.


