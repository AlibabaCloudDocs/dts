# View the connection status and performance of incremental data migration

This topic describes how to view the connection status and performance of incremental data migration in the Data Transmission Service \(DTS\) console. DTS provides the following connection and performance metrics: the number of DDL operations, number of slow SQL statements in the destination database, bandwidth, migration performance, and migration latency. You can monitor and manage data migration tasks by using these metrics.

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Data Migration**.

3.  At the top of the Migration Tasks page, select the region where the destination instance resides.

    ![Select a region](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  On the Migration Tasks page, click the ID of the destination instance.

5.  In the left-side navigation pane, choose **Performance Monitoring** \> **Performance of Incremental Data Migration**.

6.  On the page that appears, view the topology of the migration task. You can also select a time range to view the trend charts of performance metrics.

    ![Topology of incremental data migration](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4302973061/p129679.png)

    |Section|Description|
    |-------|-----------|
    |Topology of Incremental Data Migration|In this section, you can view the data transmission status and network latency between DTS modules. The DTS modules include the source database, data collection module, data caching module, data writing module, and destination database. The following parameters are provided:     -   **BPS**: the bandwidth between DTS modules. Unit: MB/s.
    -   **RPS**: the number of records that are transmitted between DTS modules per second.
    -   **Network Latency**: the network latency between DTS modules. |
    |Performance of Incremental Data Migration|In this section, you can view the bandwidth, migration performance, and migration latency. You can also select other metrics from the **More Metrics** drop-down list. The following metrics are provided:

     -   **Bandwidth**: the amount of data that is written to the destination database per second. Unit: MB/s.
    -   **Migration Performance \(RPS\)**: the number of records that are written to the destination database per second.
    -   **Migration Latency \(s\)**: the difference between the time when the latest data record is migrated to the destination database and the time when the data record is processed in the source database.
    -   **Number of DDL Operations**: the number of DDL operations that are executed in the destination database within the selected time range.
    -   **Latency \(ms\)**: the difference between the time when a data record is generated in the source database and the time when the data record is written to the destination database. For example, if a data record is generated in the source database at 7 o'clock and DTS writes the data record to the destination database at 8 o'clock, the task is delayed by 1 hour.
    -   **Slow SQL Queries**: the number of slow SQL statements that are executed in the destination database within the selected time range. |


