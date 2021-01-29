# View tracked data changes

In the Data Transmission Service \(DTS\) console, you can view the data changes that are tracked by a change tracking task. This topic provides the definition of each field in the tracked data changes.

## Prerequisites

-   A change tracking task is created. For more information, see [Track data changes from an ApsaraDB RDS for MySQL instance \(previous\)](/intl.en-US/Change Tracking/Change tracking (previous)/Track data changes from an ApsaraDB RDS for MySQL instance (previous).md).
-   The change tracking task is in the Normal or Error state.

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).
2.  In the left-side navigation pane, click **Change Tracking**.
3.  At the top of the Change Tracking Tasks page, select the region where the change tracking instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3583097951/p51699.png)

4.  Find the change tracking instance and click the instance ID.
5.  In the left-side navigation pane, click **Track Data Changes**.
6.  On the Track Data Changes page, 20 data entries that are tracked in the last minute are displayed by default. You can specify conditions to filter the tracked data changes.

    ![View tracked data changes](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9483097951/p53111.png)

    |Filtering condition|Description|
    |:------------------|:----------|
    |Database Name|Enter the name of the database that you want to query. If you do not specify this condition, all databases are queried.|
    |Table Name|Enter the name of the table that you want to query. If you do not specify this condition, all tables are queried.|
    |Column Name|Enter the name of the column that you want to query, select an operator, and then enter the column value. If you do not specify this condition, all columns are queried. **Note:** You can click the plus sign \(![Plus sign](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9483097951/p53105.png)\) to add more column filters.

 ![Set a column filter](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9483097951/p53104.png) |
    |SQL Type|Select the types of SQL operations that you want to query. INSERT, UPDATE, DELETE, and DDL operations are queried by default.|
    |Time|Select a time range. The selected time range must be within the time range of all tracked data changes. For more information, see the prompt at the top of the page.

 ![Select a time range](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9483097951/p53110.png) |

7.  Click **Search** to retrieve specific data entries.

    ![Retrieve specific data entries](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9483097951/p53149.png)

    |Database Type|Consumption checkpoint|Timestamp|
    |:------------|:---------------------|:--------|
    |RDS MySQL|The location and time of an incremental data entry in the binary log.|The timestamp when an incremental data entry is written to the binary log file.|
    |Oracle|The location and time of an incremental data entry in the redo or archive log.|The timestamp when an incremental data entry is written to the redo or archive log.|

8.  Click **View Details** in the Actions column of a data entry to view the details.

    ![View the details of a data entry.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9483097951/p53124.png)

    |Field|Description|
    |:----|:----------|
    |Field Name|The name of the field or column.|
    |Field Type|The type of the field.|
    |Character Encoding|The character encoding of the field, such as UTF-8, GBK, Latin 1, or UTF-8 \(MB4\).|
    |Pre-image|The value of each field before the data entry is updated.|
    |Post-image|The value of each field after the data entry is updated. The updated values are displayed in red.|


## Additional information about the pre-image and post-image

|Operation type|Value description|
|:-------------|:----------------|
|INSERT|![Details of an INSERT operation](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0583097951/p53162.png)

The value of the pre-image is empty.

 The value of the post-image is the data written by the INSERT operation. |
|DELETE|![Details of a DELETE operation](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0583097951/p53163.png)

The value of the pre-image is the data deleted by the DELETE operation.

 The value of the post-image is empty. |
|UPDATE|![Details of an UPDATE operation](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0583097951/p53165.png)

The value of the pre-image is the data before update.

 The value of the post-image is the data after update.

 **Note:** The updated values are displayed in red. |
|DDL or DML|![Details of a DDL or DML operation](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0583097951/p53164.png)

The details contain only DDL or DML statements.|

