# Configure RAM authorization for data migration or synchronization from a user-created database in a VPC across different Alibaba Cloud accounts

This topic describes how to configure Resource Access Management \(RAM\) authorization for data migration or synchronization from a user-created database in a virtual private cloud \(VPC\) across different Alibaba Cloud accounts. After authorization, Data Transmission Service \(DTS\) can read data from a VPC that belongs to another Alibaba Cloud account when you configure data migration or synchronization. You can migrate or synchronize data from a user-created database that is connected over Express Connect across different Alibaba Cloud accounts.

The Alibaba Cloud account to which the Express Connect circuit belongs has authorized the RAM role of DTS to access the cloud resources of the account. For more information, see [Authorize DTS to access Alibaba Cloud resources](/intl.en-US/RAM-based Access Control/Authorize DTS to access Alibaba Cloud resources.md).

An on-premises database or a database hosted on a third-party cloud is connected to Alibaba Cloud VPC over Express Connect, VPN Gateway, or Smart Access Gateway. You need to migrate data from the on-premises database or the database hosted on the third-party cloud to an ApsaraDB RDS instance across different Alibaba Cloud accounts. The following figure shows the architecture for this scenario.

**Note:** Before you can use DTS to migrate or synchronize data from a user-created database in a VPC cross different Alibaba Cloud accounts, you must perform the following steps: Configure RAM authorization for the Alibaba Cloud account to which the Express Connect circuit belongs \(Account A\), specify the Alibaba Cloud account to which the destination instance belongs \(Account B\) as a trusted account, and then authorize Account B to access the cloud resources of Account A.

![Background information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1624948951/p66757.png)

## Step 1: Create a RAM role and grant the default permission on DTS to the role

1.  Log on to the [RAM console](https://ram.console.aliyun.com/) with the Alibaba Cloud account to which the Express Connect circuit belongs.

2.  In the left-side navigation pane, click **RAM Roles**.

3.  Click **Create RAM Role**, select **Alibaba Cloud Account**, and then click **Next**.

4.  In the Create RAM Role panel, configure parameters for the RAM role.

    ![Configure parameters for the RAM role](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6083097951/p44837.png)

    |Parameter|Description|
    |:--------|:----------|
    |RAM Role Name|Specify a name for the RAM role. In this example, enter **ram-for-dts**. **Note:** The name must be 1 to 64 characters in length and can contain letters, digits, and hyphens \(-\). |
    |Note|Optional. Specify the description for the RAM role.|
    |Select Trusted Alibaba Cloud Account|Select **Other Alibaba Cloud Account** and enter the ID of the Alibaba Cloud account to which the destination instance belongs. **Note:** To obtain the ID of the Alibaba Cloud account to which the destination instance belongs, you must log on to the [Account Management](https://account.console.aliyun.com/#/secure) console with this account. The account ID is displayed on the Security Settings page.

![Obtain the ID of the Alibaba Cloud account](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4634948951/p44838.png) |

5.  Click **OK**.

6.  Click **Input and Attach**.

7.  In the Add Permissions panel, select **System Policy** and enter **AliyunDTSRolePolicy** in the Policy Name field.

    ![Grant permissions](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6083097951/p44840.png)

8.  Click **OK**.

9.  Click **Close**.


## Step 2: Authorize the RAM role to access the VPC under another Alibaba Cloud account

1.  Log on to the [RAM console](https://ram.console.aliyun.com/) with the Alibaba Cloud account to which the Express Connect circuit belongs.

2.  In the left-side navigation pane, click **RAM Roles**.

3.  Find the RAM role created in [Step 1](#section_bsu_lje_o5s), and click the role name.

    ![Click a RAM role name](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6083097951/p66567.png)

4.  On the Basic Information page of the RAM role, click Add Permissions.

5.  In the Add Permissions panel, enter **AliyunVPCReadOnlyAccess** in the search box and click the policy name to move the policy to the **Selected** section.

    ![Grant permissions](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6083097951/p66568.png)

6.  Click **OK**.

7.  On the Basic Information page of the RAM role, click the Trust Policy Management tab.

8.  Click **Edit Trust Policy**, and replace the policy text with the following sample statements.

    ![Modify the trust policy](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6083097951/p67506.png)

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

    **Note:** To obtain the ID of the Alibaba Cloud account to which the destination instance belongs, you must log on to the [Account Management](https://account.console.aliyun.com/#/secure) console with this account. The account ID is displayed on the Security Settings page. Then, you must replace the `<ID of the Alibaba Cloud account to which the destination instance belongs>` in the preceding statements with the account ID.

    ![Obtain an Alibaba Cloud account ID](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4634948951/p44838.png)


## References

-   [Migrate data from a user-created MySQL database connected over Express Connect, VPN Gateway, or Smart Access Gateway to an ApsaraDB RDS for MySQL instance across Alibaba Cloud accounts](/intl.en-US/Data Migration/Migration from a user-created database to Alibaba Cloud/Source database: MySQL/Migrate data from a user-created MySQL database connected over Express Connect, VPN Gateway, or Smart Access Gateway to an ApsaraDB RDS for MySQL instance across Alibaba Cloud accounts.md)
-   [Synchronize data between ApsaraDB for Redis cluster instances across Alibaba Cloud accounts](/intl.en-US/Data Synchronization/Synchronize data between Redis databases/Synchronize data between ApsaraDB for Redis cluster instances across Alibaba Cloud
         accounts.md)

