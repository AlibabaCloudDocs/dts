# Configure a data integration task between ApsaraDB RDS for MySQL instances

This topic describes how to configure a data integration task between ApsaraDB RDS for MySQL instances.

**Note:** All databases that support full data migration support the data integration feature. For more information, see [Supported databases and migration types](/intl.en-US/Data Migration/Supported databases and migration types.md).

## Prerequisites

The source and destination ApsaraDB RDS for MySQL instances are created. For more information, see [Create an ApsaraDB RDS for MySQL instance](http://www.alibabacloud.com/help/zh/doc-detail/26117.htm).

## Background information

Data Transmission Service \(DTS\) provides the data integration feature. You can configure a scheduling policy when you migrate schemas and historical data from the source database to the destination database on a regular basis. The data integration feature allows you to flexibly build data warehouses. For more information, see [Scheduling policies of data integration](/intl.en-US/Data Integration/Scheduling policies of regular data migration.md).

## Precautions

The source database must have PRIMARY KEY or UNIQUE constraints and all fields must be unique. Otherwise, the destination database may contain duplicate data records.

## Limits

-   DTS supports schema migration for the following types of objects: table, view, trigger, stored procedure, and function.

    **Note:** During schema migration, DTS changes the value of the SECURITY attribute from `DEFINER` to `INVOKER` for views, stored procedures, and functions.

-   DTS does not migrate user information from the source database. Before a user can call views, stored procedures, and functions of the destination database, you must grant the read/write permissions to the user.

## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|--------------|--------------------------|--------------------|
|Schema migration and full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|

## Procedure

The procedure in this topic uses ApsaraDB RDS for MySQL instances that reside in the China \(Qingdao\) region as an example.

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Data Integration**.

3.  At the top of the Migration Tasks page, select the region where the destination instance resides. In this example, select the China \(Qingdao\) region.

4.  In the upper-right corner of the page, click **Create Data Integration Task**.

5.  Configure the source and destination databases.

    ![Migrate data between ApsaraDB RDS for MySQL instances](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0567407951/p81158.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|Instance Type|Select **RDS Instance**.|
    |Instance Region|Select the region where the source RDS instance resides. In this example, select **China \(Qingdao\)**.|
    |Database Account|Enter the database account of the source RDS instance. The account must have the read and write permissions on the source database.**Note:** For more information about how to create and authorize a database account, see [Create an account on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96089.htm). |
    |Database Password|Enter the password of the source database account. **Note:** After you specify the source database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
    |Encryption|Select **Non-encrypted** or **SSL-encrypted**. If you want to select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before you configure the data migration task. For more information, see [Configure SSL encryption on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96120.htm). **Note:** The **Encryption** parameter is available only for regions in mainland China and the China \(Hong Kong\) region. |
    |Destination Database|Instance Type|Select **RDS Instance**.|
    |Instance Region|Select the region where the destination RDS instance resides. In this example, select **China \(Qingdao\)**.|
    |Database Account|Enter the database account of the destination RDS instance. The account must have the read and write permissions on the destination database.**Note:** For more information about how to create and authorize a database account, see [Create an account on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96089.htm). |
    |Database Password|Enter the password of the destination database account. **Note:** After you specify the destination database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the destination database parameters based on the check results. |
    |Encryption|Select **Non-encrypted** or **SSL-encrypted**. If you want to select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before you configure the data migration task. For more information, see [Configure SSL encryption on an ApsaraDB RDS for MySQL instance](https://www.alibabacloud.com/help/zh/doc-detail/96120.htm). **Note:** The **Encryption** parameter is available only for regions in mainland China and the China \(Hong Kong\) region. |

6.  In the lower-right corner of the page, click **Set Whitelist and Next**.

7.  Configure a scheduling policy and select the objects to be migrated.

    1.  Configure a scheduling policy.

        ![Configure a data integration task](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5524973061/p130801.png)

        |Parameter|Description|
        |---------|-----------|
        |**Migration Types**|Select **Schema Migration** and **Full Data Migration**. DTS migrates the schemas and historical data of the required objects from the source database to the destination database.|
        |**Scheduling Policy**|        -   **Multi-replica Mode**
        -   **Resynchronization Mode**
        -   **Timestamp-based Incremental Data Mode**
**Note:** For more information about the scheduling policies, see [Scheduling policies](/intl.en-US/Data Integration/Scheduling policies of regular data migration.md). |
        |**Effective Duration**|Click the ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6971171061/p107762.png) icon, and specify the start date and end date of the schedule. The maximum effective duration is 365 days.|
        |**Scheduling Cycle**|Select **Day** or **Week** as the scheduling cycle and set the specific scheduled time. **Note:**

        -   During full data migration, DTS uses read and write resources of the source and destination databases. This may increase the loads of the database servers. Before you start scheduling, evaluate the impact of scheduling on the performance of the source and destination databases. We recommend that you set the scheduled time to off-peak hours.
        -   After you complete the settings, click **View Scheduled Time** next to **Task Failure Policy** to check whether the set scheduled time meets your expectation. |
        |**Task Failure Policy**|Select one of the following policies based on your business requirements:         -   **Do not run subsequent scheduling tasks if a task fails**
        -   **Ignore a failed scheduling task and run subsequent scheduling tasks** |

    2.  Select the objects to be migrated.

        ![Select the objects to be migrated](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0567407951/p81173.png)

        |Setting|Description|
        |:------|:----------|
        |**Select the objects to be migrated**|Select objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section. You can select columns, tables, or databases as the objects to be migrated.

**Note:**

        -   If you set **Scheduling Policy** to **Timestamp-based Incremental Data Mode**, you can select only columns or tables as the objects to be migrated. If you select databases, you cannot specify the timestamp field in the next step.
        -   After an object is migrated to the destination instance, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are migrated to the destination instance. For more information, see [Object name mapping](/intl.en-US/Data Migration/Migration task management/Object name mapping.md).
        -   If you use the object name mapping feature on an object, other objects that are dependent on the object may fail to be migrated. |

    3.  In the lower-right corner of the page, click **Next**. On the page that appears, specify the timestamp field.

        **Note:** This step is required only if you set **Scheduling Policy** to **Timestamp-based Incremental Data Mode**.

        ![Specify the timestamp field](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1567407951/p93762.png)

8.  In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data migration task, a precheck is performed. A data migration task can be started only if it passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and perform a precheck again.
9.  After the task passes the precheck, click **Next**.

10. In the Confirm Settings dialog box, specify the **Channel Specification** parameter and select **Data Transmission Service \(Pay-As-You-Go\) Service Terms**.

11. Click **Buy and Start**. The data integration task runs based on the specified scheduled time.

    ![Status of a data integration task](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6524973061/p130814.png)

    **Note:** The data integration task automatically stops at the end of the scheduled time. To stop the task before the end time of the schedule, click **Stop Data Integration Task**.


If a task is completed before the end of the current scheduled time, the task status changes to **Completed**. DTS waits for the next scheduled time and performs full data migration again.

![A running data integration task](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6524973061/p130816.png)

