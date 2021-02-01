# Migrate data between RDS instances of different Alibaba Cloud accounts

This topic describes how to migrate data between RDS instances of different Alibaba Cloud accounts by using Data Transmission Service \(DTS\).

## Prerequisites

The available storage space of the destination instance is larger than the total size of the data in the source instance.

## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|--------------|--------------------------|--------------------|
|Schema migration and full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|
|Incremental data migration|Charged. For more information, see [Pricing]().|

## Permissions required for database accounts

|Instance type|Schema migration|Full data migration|Incremental data migration|
|:------------|:---------------|:------------------|:-------------------------|
|Source RDS instance|The read and write permissions|The read and write permissions|The read and write permissions|
|Destination RDS instance|The read and write permissions|The read and write permissions|The read and write permissions|

## Before you begin

Configure Resource Access Management \(RAM\) authorization for the Alibaba Cloud account to which the source instance belongs. Specify the Alibaba Cloud account to which the destination instance belongs as a trusted account. The settings ensure that the destination account can access cloud resources of the Alibaba Cloud account to which the source instance belongs. For more information, see [Configure RAM authorization for cross-account data migration and synchronization](/intl.en-US/RAM-based Access Control/Configure RAM authorization for cross-account data migration and synchronization.md).

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/) with the Alibaba Cloud account to which the destination instance belongs.
2.  In the left-side navigation pane, click **Data Migration**.
3.  At the top of the Migration Tasks page, select the region where the destination RDS instance resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.
5.  In the **Source Database** section, set Instance Type to **RDS Instance**, and click **RDS Instances of Other Apsara Stack Accounts** next to the RDS Instance ID field.

    ![Cross-account configuration](../images/p207363.png)

6.  Configure the **source and destination databases** for the data migration task.

    ![Configure the source and destination databases](../images/p207361.png)

    |Parameter|Description|
    |---------|-----------|
    |Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|    -   Instance Type: Select **RDS Instance**.

**Note:** If you have selected **RDS Instance** as the instance type, you can skip this step.

    -   Instance Region: Select the region where the source RDS instance resides.

**Note:** You can select different regions for the source and destination RDS instances.

    -   Apsara Stack Tenant Account ID of RDS Instance: Enter the ID of the Alibaba Cloud account to which the source instance belongs.

**Note:** To obtain the ID of the Alibaba Cloud account to which the source instance belongs, you must log on to the [Account Management](https://account.console.aliyun.com/#/secure) console with this account. The account ID is displayed on the Security Settings page.

    -   Role Name: Enter the role name configured for the Alibaba Cloud account to which the source instance belongs. For more information, see [Configure RAM authorization for cross-account data migration and synchronization](/intl.en-US/RAM-based Access Control/Configure RAM authorization for cross-account data migration and synchronization.md).
    -   RDS Instance ID: Select the ID of the source RDS instance.

**Note:** If an alert message appears when you select an RDS instance ID, modify the parameter values as prompted. For more information, see [FAQ](#section_llh_0pc_won).

    -   Database Account: Enter the database account of the source RDS instance. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_lcj_kcb_mhb).
    -   Database Password: Enter the password of the source database account. |
    |Destination Database|    -   Instance Type: Select **RDS Instance**.

**Note:** You can select different regions for the source and destination RDS instances.

    -   Instance Region: Select the region where the destination RDS instance resides.
    -   RDS instance ID: Select the ID of the destination RDS instance.
    -   Database Account: Enter the database account of the destination RDS instance. For more information about the permissions that are required for the account, see [Permissions required for database accounts](#section_lcj_kcb_mhb).
    -   Database Password: Enter the password of the destination database account.
    -   Encryption: Select **Non-encrypted** or **SSL-encrypted**. In this example, select **Non-encrypted**.

**Note:** If you want to select **SSL-encrypted**, you must enable SSL encryption for the RDS instance before you configure the data migration task. For more information, see [Configure SSL encryption on an ApsaraDB RDS for MySQL instance](~~96120~~). |

7.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelists of the source and destination RDS instances. This ensures that DTS servers can connect to the source and destination RDS instances.

8.  Select the migration types and the objects to be migrated.

    ![Select the migration types and the objects to be migrated](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8906544061/p47745.png)

    |Setting|Description|
    |:------|:----------|
    |Select the migration types|    -   To perform only full data migration, select **Schema Migration** and **Full Data Migration**.
    -   To ensure service continuity during data migration, select **Schema Migration**, **Full Data Migration**, and **Incremental Data Migration**.
**Note:** If **Incremental Data Migration** is not selected, do not write data to the source database during full data migration. This ensures data consistency between the source and destination databases. |
    |Select the objects to be migrated|Select objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section.

**Note:**

    -   You can select columns, tables, or databases as the objects to be migrated. If you select tables or columns as the objects to be migrated, DTS does not migrate other objects such as views, triggers, and stored procedures to the destination database.
    -   After an object is migrated to the destination database, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are migrated to the destination database. For more information, see [Object name mapping](/intl.en-US/Data Migration/Migration task management/Object name mapping.md).
    -   If you use the object name mapping feature on an object, other objects that are dependent on the object may fail to be migrated. |
    |Specify the retry time for failed connection to the source or destination database|By default, if DTS fails to connect to the source or destination database, DTS retries within the next 12 hours. You can specify the retry time based on your needs. If DTS reconnects to the source and destination databases within the specified time, DTS resumes the data migration task. Otherwise, the data migration task fails.**Note:** When DTS retries a connection, you are charged for the DTS instance. We recommend that you specify the retry time based on your business needs. You can also release the DTS instance at your earliest opportunity after the source and destination instances are released. |

9.  In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data migration task, a precheck is performed. You can start the data migration task only after the task passes the precheck.
    -   If the task fails to pass the precheck, click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Troubleshoot the issues based on the causes and run the precheck again.
10. After the task passes the precheck, click **Next**.
11. In the Confirm Settings dialog box, specify the **Channel Specification** and select **Data Transmission Service \(Pay-As-You-Go\) Service Terms**.
12. Click **Buy and Start** to start the migration task.
    -   Full data migration

        Do not manually stop a task during full data migration. Otherwise, data migrated to the destination database will be incomplete. Wait until the migration task automatically stops.

    -   Incremental data migration

        An incremental data migration task does not automatically stop. You must manually stop the migration task.

        **Note:** Select an appropriate time to manually stop the migration task. For example, you can stop the migration task during off-peak hours or before you switch your workloads to the destination instance.

        1.  Wait until **Incremental Data Migration** and **The migration task is not delayed** appear in the progress bar of the migration task. Then, stop writing data to the source database for a few minutes. The delay time of **incremental data migration** may be displayed in the progress bar.
        2.  After the status of **incremental data migration** changes to **The migration task is not delayed**, manually stop the migration task.

            ![The migration task is not delayed](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2940359951/p47604.png)


## FAQ

If an alert message appears when you set the RDS Instance ID parameter for the source database, you can solve the issue as described in following table.

|Alert message|Solution|
|-------------|--------|
|![Alert message_Role does not exist](../images/p207433.png)

|Check whether the ID of the Alibaba Cloud account to which the source instance belongs and the role name configured for the account are valid. **Note:** To obtain the ID of the Alibaba Cloud account to which the source instance belongs, you must log on to the [Account Management](https://account.console.aliyun.com/#/secure) console with this account. The account ID is displayed on the Security Settings page. |
|![Alert message_No permissions](../images/p207434.png)

|Make sure that the following operations are performed: 1. Configure RAM authorization for the Alibaba Cloud account to which the source instance belongs. 2. Specify the Alibaba Cloud account to which the destination instance belongs as a trusted account. The settings ensure that the destination account can access cloud resources of the Alibaba Cloud account to which the source instance belongs. For more information, see [Configure RAM authorization for cross-account data migration and synchronization](/intl.en-US/RAM-based Access Control/Configure RAM authorization for cross-account data migration and synchronization.md).|
| | |

