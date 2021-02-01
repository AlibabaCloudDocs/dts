# Configure RAM authorization for cross-account data migration and synchronization

Data Transmission Service \(DTS\) supports data migration and synchronization between RDS instances that belong to different Alibaba Cloud accounts. This topic describes how to configure Resource Access Management \(RAM\) authorization for the Alibaba Cloud account to which the source instance belongs if the destination instance belongs to a different Alibaba Cloud account.

## Prerequisites

The Alibaba Cloud account to which the source instance belongs has authorized the RAM role of DTS to access the cloud resources of the account. For more information, see [Authorize DTS to access Alibaba Cloud resources](/intl.en-US/RAM-based Access Control/Authorize DTS to access Alibaba Cloud resources.md).

## Instance types supported by cross-account data migration and synchronization

|Feature|Source instance|Destination instance|
|:------|:--------------|:-------------------|
|Data migration|RDS instance|RDS instance|
|DRDS instance|
|HybridDB for MySQL instance|
|ApsaraDB for OceanBase instance|
|User-created database hosted on Elastic Compute Service \(ECS\)|
|User-created database with a public IP address|
|Data synchronization|RDS instance|RDS instance|
|MaxCompute \(previous name: ODPS\) instance|
|Elasticsearch instance|

## Background information

When you use DTS to migrate or synchronize data, you must configure RAM authorization for the Alibaba Cloud account to which the source instance belongs. You must specify the Alibaba Cloud account to which the destination instance belongs as a trusted account. The settings ensure that the destination account can access cloud resources of the Alibaba Cloud account to which the source instance belongs.

**Note:** After authorization, you can create a data migration task or data synchronization task by using the Alibaba Cloud account to which the destination instance belongs.

## Procedure

1.  Log on to the [RAM console](https://ram.console.aliyun.com/) with the Alibaba Cloud account to which the source instance belongs.
2.  In the left-side navigation pane, click **RAM Roles**.
3.  Click **Create RAM Role**, select **Alibaba Cloud Account** as the trusted entity, and then click **Next**.
4.  In the Create RAM Role dialog box, configure parameters for the RAM role.

    ![Configure parameters for the RAM role](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6083097951/p44837.png)

    |Parameter|Description|
    |:--------|:----------|
    |RAM Role Name|Specify a name for the RAM role. In this example, enter **ram-for-dts**. **Note:** The name must be 1 to 64 characters in length and can contain letters, digits, and hyphens \(-\). |
    |Note|Optional. Specify the description for the RAM role.|
    |Select Trusted Alibaba Cloud Account|Select **Other Alibaba Cloud Account** and enter the ID of the Alibaba Cloud account to which the destination instance belongs. **Note:** To obtain the ID of the Alibaba Cloud account to which the destination instance belongs, you must log on to the [Account Management](https://account.console.aliyun.com/#/secure) console with this account. The account ID is displayed on the Security Settings page.

![Obtain an Alibaba Cloud account ID](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4634948951/p44838.png) |

5.  Click **OK**.
6.  Click **Input and Attach**.

    ![Click Input and Attach](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4083097951/p120781.png)

7.  On the Add Permissions page, select **System Policy** and enter **AliyunDTSRolePolicy**.

    ![Grant permissions](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6083097951/p44840.png)

8.  Click **OK**.
9.  Click **Close**.
10. On the **RAM Roles** page, find the RAM role that you created, and click the role name to view details.

    ![Click a RAM role name](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6083097951/p66567.png)

11. On the Basic Information page of the RAM role, click the Trust Policy Management tab.
12. On the Trust Policy Management tab, click **Edit Trust Policy**, and copy the following sample statements to the page that appears.

    ![Edit the trust policy](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6083097951/p67506.png)

    ```
    {
        "Statement": [
            {
                "Action": "sts:AssumeRole",
                "Effect": "Allow",
                "Principal": {
                    "RAM": [
                        "acs:ram::<ID of the Alibaba Cloud account to which the destination instance belongs>:root"
                    ],
                    "Service": [
                        "<ID of the Alibaba Cloud account to which the destination instance belongs>@dts.aliyuncs.com"
                    ]
                }
            }
        ],
        "Version": "1"
    }
    ```

    **Note:** To obtain the ID of the Alibaba Cloud account to which the destination instance belongs, you must log on to the [Account Management](https://account.console.aliyun.com/#/secure) console with this account. The account ID is displayed on the Security Settings page. Then, you must replace the `<ID of the Alibaba Cloud account to which the destination instance belongs>` in the preceding statements with the obtained account ID.

    ![Obtain an Alibaba Cloud account ID](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4634948951/p44838.png)


After authorization, you can create a task to migrate or synchronize data between RDS instances that belong to different Alibaba Cloud accounts.

## What to do next

Log on to the [DTS console](https://dts-intl.console.aliyun.com/) with the Alibaba Cloud account to which the destination instance belongs. Then, create a data migration task or data synchronization task.

**Note:** For more information about how to configure a data synchronization task, see [Synchronize data between ApsaraDB RDS for MySQL instances that belong to different Alibaba Cloud accounts](/intl.en-US/Data Synchronization/Synchronize data between MySQL databases/Synchronize data between ApsaraDB RDS for MySQL instances that belong to different
         Alibaba Cloud accounts.md).

