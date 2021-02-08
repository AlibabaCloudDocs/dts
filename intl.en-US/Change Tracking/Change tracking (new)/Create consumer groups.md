# Create consumer groups

You can create multiple consumer groups when you use the change tracking feature. Consumers in different consumer groups can track data changes from the same data source. Consumer groups allow you to reduce usage costs and improve the efficiency of data consumption.

## Limits

-   You can create a maximum of 20 consumer groups in a change tracking instance to achieve repeated data consumption.
-   You can create only one consumer in each consumer group.

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).
2.  In the left-side navigation pane, click **Change Tracking**.
3.  At the top of the Change Tracking Tasks page, select the region where the change tracking instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3583097951/p51699.png)

4.  Find the change tracking instance and click the instance ID.

    ![Click the instance ID](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6483097951/p48136.png)

5.  In the left-side navigation pane, click **Data Consume**.
6.  On the Data Consume page, click **Add consumer group** in the upper-right corner.

    ![Add a consumer group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5483097951/p48139.png)

7.  In the Create consumer group dialog box, set parameters for the consumer group.

    ![Create a consumer group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5483097951/p48835.png)

    |Parameter|Description|
    |:--------|:----------|
    |Consumer group name|Enter a new name for the consumer group. We recommend that you use an informative name for easy identification.|
    |Username|Enter the username of the consumer group.     -   A username must contain one or more of the following characters: uppercase letters, lowercase letters, digits, and underscores \(\_\).
    -   A username must be 1 to 16 characters in length. |
    |Password|Enter the password that corresponds to the username of the consumer group.     -   A password must contain two or more of the following characters: uppercase letters, lowercase letters, digits, and special characters.
    -   A password must be 8 to 32 characters in length. |
    |Confirm password|Enter the password again.|

8.  Click **Create**.

## What to do next

[Use a Kafka client to consume tracked data](/intl.en-US/Change Tracking/Change tracking (new)/Use a Kafka client to consume tracked data.md)

