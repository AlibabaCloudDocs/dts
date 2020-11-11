# View the connection status and performance of full data migration

This topic describes how to view the connection status and performance of full data migration in the Data Transmission Service \(DTS\) console. DTS provides the following connection and performance metrics: bytes per second \(BPS\), records per second \(RPS\), read/write response time, and network latency. You can monitor and manage data migration tasks by using these metrics.

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Data Migration**.

3.  At the top of the Migration Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  On the Migration Tasks page, click the ID of the destination instance.

5.  In the left-side navigation pane, choose **Performance Monitoring** \> **Performance of Full Data Migration**.

6.  On the page that appears, the connection status and performance of full data migration are displayed. You can select a time range to view the trend charts of performance metrics for full data migration.

    ![Performance of full data migration](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1302973061/p129446.png)

    |Section|Description|
    |-------|-----------|
    |Topology of Full Data Migration|In this section, you can view the read/write performance and network information about the connections between DTS and the source and destination databases. The following parameters are provided:     -   Connection between DTS and the source database
        -   **BPS**: the amount of data that DTS reads from the source database per second. Unit: MB/s.
        -   **RPS**: the number of records that DTS reads from the source database per second.
        -   **Network Latency**: the network latency between DTS and the source database.
    -   Connection between DTS and the destination database
        -   **BPS**: the amount of data that DTS writes to the destination database per second. Unit: MB/s.
        -   **RPS**: the number of records that DTS writes to the destination database per second.
        -   **Network Latency**: the network latency between DTS and the destination database. |
    |Performance of Full Data Migration|In this section, you can view the bandwidth, records per second \(RPS\), read/write response time, and network latency. **Note:** To view the description of performance metrics, move the pointer over the **Metrics** button at the upper-right corner of a trend chart. |


