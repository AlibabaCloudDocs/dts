# Use a custom policy to authorize a RAM user to manage DTS instances

This topic describes how to create a custom policy. Custom policies provide more fine-grained permission control than system policies. You can create a custom policy to control the permissions on specific instances or operations.

A RAM user is authorized to access cloud resources. For more information, see [Authorize DTS to access Alibaba Cloud resources](/intl.en-US/RAM-based Access Control/Authorize DTS to access Alibaba Cloud resources.md).

A policy defines a set of permissions that are described based on the policy structure and syntax. A policy describes the authorized resource sets, authorized operation sets, and authorization conditions. For more information, see [Policy structure and syntax](https://www.alibabacloud.com/help/zh/doc-detail/93739.htm).

## Precautions

-   If you need to synchronize data to MaxCompute, you cannot use a RAM user to configure the data synchronization task. You must use an Alibaba Cloud account to configure the task.
-   If you use a RAM user to configure DTS tasks and the database is connected over Database Gateway, you must grant the **AliyunDGFullAccess** permission to the RAM user. If you use a RAM user to configure DTS tasks and the database is connected over Cloud Enterprise Network \(CEN\), you must grant the **AliyunCENFullAccess** permission to the RAM user.

## Step 1: Create a custom policy

1.  Log on to the [RAM console](https://ram.console.aliyun.com/) by using an Alibaba Cloud account.

2.  In the left-side navigation pane, click **Policies** under **Permissions**.

3.  On the Policies page, click **Create Policy**.

4.  Configure parameters for the custom policy.

    ![Create a custom policy](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2083097951/p76495.png)

    |Parameter|Description|
    |---------|-----------|
    |Policy Name|Enter an informative name for easy identification.|
    |Note|Optional. Enter the description of the policy.|
    |Configuration Mode|Select **Script**. To configure policies for DTS, you must select **Script**.|
    |Policy Document|Select an existing system policy from the drop-down list. **Note:** This topic describes how to create a custom policy. You do not need to specify this parameter. |
    |Code Editor|Enter the content of the policy in the code editor. Sample custom policies are provided for your reference below this table. **Note:**

    -   A policy defines a set of permissions that are described based on the policy structure and syntax. A policy describes the authorized resource sets, the authorized operation sets, and the authorization conditions. For more information, see [Policy structure and syntax](https://www.alibabacloud.com/help/zh/doc-detail/93739.htm).
    -   You can grant permissions on specific resources and actions. |

    Sample custom policies:

    **Note:**

    -   You must replace the `DTS instance ID` in the following code with the actual ID of your DTS instance.
    -   If the read-only permission on a DTS instance is granted to a RAM user, the RAM user can query task details and configurations but cannot change configurations. If the read and write permissions on a DTS instance are granted to a RAM user, the RAM user can configure and manage the DTS instance.
    -   Example 1: Read-only permission on a single DTS instance
    -   Example 2: Read and write permissions on multiple DTS instances
    -   Example 3: View the configurations of a data synchronization task
    -   Example 4: Start or pause multiple data synchronization tasks
5.  Click **OK**.


## Step 2: Attach the custom policy to a RAM user

1.  Log on to the [RAM console](https://ram.console.aliyun.com/) by using an Alibaba Cloud account.

2.  [Create a RAM user](/intl.en-US/RAM User Management/Create a RAM user.md).

3.  In the left-side navigation pane, choose **Identities** \> **Users**.

4.  In the **User Logon Name/Display Name** column, find the target RAM user.

5.  Click **Add Permissions** in the Actions column.

    ![Add permissions](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3083097951/p45025.png)

6.  In the Add Permissions pane, select the required permission policies.

    1.  Select **Custom Policy**.

    2.  Click the name of a custom policy to add the policy to the **Selected** section.

        ![Select a custom policy](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2083097951/p76507.png)

7.  Click **OK**.

8.  Click **Complete**.


## Scenarios of operation-level authorization

**Note:**

-   The `DescribeMigrationJobs`, `DescribeSubscriptionInstances`, and `DescribeSynchronizationJobs` policies authorize a RAM user to query available DTS instances. If a RAM user has the permissions only on some instances, the user must query available DTS instances before the user can perform related operations.
-   To authorize a RAM user to configure data migration, data synchronization, or change tracking, you must create a custom policy and attach the policy to the user. For more information, see [t17088.md\#section\_08b\_3g1\_3bs](/intl.en-US/RAM-based Access Control/Authorize DTS to access Alibaba Cloud resources.mdsection_08b_3g1_3bs).

|Feature|Operation in the DTS console|Permission policy|
|:------|:---------------------------|:----------------|
|Data migration|Create a data migration task|CreateMigrationJob|
|Query data migration tasks|DescribeMigrationJobs|
|View the details of a data migration task|DescribeMigrationJobs

DescribeMigrationJobDetail

DescribeMigrationJobStatus |
|Modify the name of a data migration task|DescribeMigrationJobs

ModifyMigrationObject |
|Configure a data migration task|DescribeMigrationJobs

DescribeMigrationJobDetail

DescribeMigrationJobStatus

CreateMigrationJob |
|View precheck details|DescribeMigrationJobs

DescribeMigrationJobStatus |
|Create a similar data migration task|DescribeMigrationJobs

DescribeMigrationJobDetail

DescribeMigrationJobStatus

CreateMigrationJob |
|Monitor a data migration task and set alerts|DescribeMigrationJobs

DescribeMigrationJobAlert

ConfigureMigrationJobAlert |
|Change the password that is used to log on to an instance|DescribeMigrationJobs

DescribeMigrationJobDetail

ModifyMigrationObject |
|Start a data migration task|DescribeMigrationJobs

StartMigrationJob

DescribeMigrationJobDetail |
|Pause a data migration task|DescribeMigrationJobs

SuspendMigrationJob |
|View the details of schema migration|DescribeMigrationJobs

DescribeMigrationJobStatus |
|View the details of full data migration|DescribeMigrationJobs

DescribeMigrationJobStatus |
|View the details of incremental data migration|DescribeMigrationJobs

DescribeMigrationJobStatus |
|View the performance of full data migration or incremental data migration|DescribeMigrationJobs

DescribeMigrationJobDetail |
|View task logs|DescribeMigrationJobs

DescribeMigrationJobDetail |
|Change tracking|Create a change tracking task|CreateSubscriptionInstance|
|Query change tracking tasks|DescribeSubscriptionInstances|
|View the details of a change tracking task|DescribeSubscriptionInstances

DescribeSubscriptionInstanceStatus |
|Modify the name of a change tracking task|DescribeSubscriptionInstances

ModifySubscriptionObject |
|Modifies the objects for change tracking.|DescribeSubscriptionInstances

DescribeSubscriptionInstanceStatus

ModifySubscriptionObject |
|Create consumer groups|DescribeSubscriptionInstances

CreateConsumerGroup |
|View the information about a consumer group|DescribeSubscriptionInstances

DescribeConsumerGroup |
|Modify the password of a consumer group|DescribeSubscriptionInstances

ModifyConsumerGroupPassword |
|Delete a consumer group|DescribeSubscriptionInstances

DeleteConsumerGroup |
|Change the password that is used to log on to an instance|DescribeSubscriptionInstances

DescribeSubscriptionInstanceStatus

ModifySubscriptionObject |
|Delete a change tracking task|DescribeSubscriptionInstances

DeleteSubscriptionInstance |
|Monitor a change tracking task and set alerts|DescribeSubscriptionInstances

DescribeSubscriptionInstanceAlert

ConfigureSubscriptionInstanceAlert |
|Configure a change tracking task|DescribeSubscriptionInstances

DescribeSubscriptionInstanceStatus

ModifySubscriptionObject |
|View task logs|DescribeSubscriptionInstances

DescribeSubscriptionInstanceStatus |
|Data synchronization|Create a data synchronization task|CreateSynchronizationJob|
|Query data synchronization tasks|DescribeSynchronizationJobs|
|View the details of a data synchronization task|DescribeSynchronizationJobs

DescribeSynchronizationJobStatus |
|Modify the name of a data synchronization task|DescribeSynchronizationJobs

ModifySynchronizationObject |
|View the configurations of a data synchronization task|DescribeSynchronizationJobs

DescribeSynchronizationJobStatus |
|View the objects to be synchronized|DescribeSynchronizationJobs

DescribeSynchronizationJobStatus |
|View the status of initial schema synchronization and initial full data synchronization|DescribeSynchronizationJobs

DescribeSynchronizationJobStatus |
|View the performance of full data synchronization or incremental data synchronization|DescribeSynchronizationJobs

DescribeSynchronizationJobStatus |
|View the modification records of the objects to be synchronized|DescribeSynchronizationJobs|
|View task logs|DescribeSynchronizationJobs

DescribeSynchronizationJobStatus |
|Configure a data synchronization task|DescribeSynchronizationJobs

DescribeSynchronizationJobStatus

ModifySynchronizationObject |
|Start a data synchronization task|DescribeSynchronizationJobs

StartSynchronizationJob |
|Pause a data synchronization task|DescribeSynchronizationJobs

SuspendSynchronizationJob |
|Modify the objects to be synchronized|DescribeSynchronizationJobs

DescribeSynchronizationJobStatus

ModifySynchronizationObject |
|Delete a data synchronization task|DescribeSynchronizationJobs

DeleteSynchronizationJob |
|Stop a data synchronization task|DescribeSynchronizationJobs

DeleteSynchronizationJob |
|Monitor a data synchronization task and set alerts|DescribeSynchronizationJobs

DescribeSynchronizationJobAlert

ConfigureSynchronizationJobAlert |
|Change the password that is used to log on to an instance|DescribeSynchronizationJobs

DescribeSynchronizationJobStatus

ModifySubscriptionObject |

## References

[Log on to the Alibaba Cloud Management Console as a RAM user](https://www.alibabacloud.com/help/zh/doc-detail/43640.htm)

## FAQ

Q: Why does an error message instead of the instance list appear when I log on to the DTS console as a RAM user?

![Error message](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2083097951/p76535.png)

A: The RAM user may have no permissions or may have permissions only on some instances. In this case, the DTS console does not show the instance list. You must contact the RAM administrator and obtain the IDs of the DTS instances on which the RAM user has administrative permissions. Then, you can search for DTS instances by using their IDs in the DTS console.

![Search for DTS instances](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2083097951/p76720.png)

