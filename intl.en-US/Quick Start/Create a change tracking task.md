# Create a change tracking task

You can follow the general procedure in this topic to create and manage a task in change tracking mode.

## Background information

-   Use cases

    The change tracking mode allows you to build a distributed application with decoupled data systems, so you can implement your asynchronous replications between these data systems.

-   Supported databases and data changes

    For more information about databases and update types that are supported by the change tracking mode, see [Supported sources and targets](/intl.en-US/Introduction/Supported sources and targets.md).

-   Advanced features

    For information about the features of a change tracking task, see [Change tracking mode](/intl.en-US/Replication Modes/Change tracking mode.md).


## Change tracking procedure

You can complete the following higher level steps to create and manage a change tracking task.

|Step|Description|
|----|-----------|
|1. Set up the networking environment|Before you use DTS for change tracking, prepare the environments that are required for the source and target databases.

For more information, see [Set up a network environment for replication](/intl.en-US/Network Setup/Set up a network environment for replication.md). |
|2. Create a change tracking instance|Create a change tracking instance in the DTS console. The instance can be either pay-as-you-go or subscription.|
|3. Create a change tracking task|Create a change tracking task for the instance that you created. When creating a task, you will set up the connection to the data source and select the objects that are to be tracked.|
|4. Configure consumer settings|-   Create consumer groups

After you create a change tracking task, create one or more consumer groups that will consume the tracked data. You can create multiple consumer groups for a change tracking task. Consumers in different consumer groups can track data changes from the same data source. For more information, see [Create a consumer group](/intl.en-US/Change Tracking/Change tracking (new)/Create a consumer group.md).

-   Manage consumer groups

Manage the consumer groups of the change tracking task. For example, you can modify passwords for consumer groups or delete consumer groups. For more information, see [Manage consumer groups](/intl.en-US/Change Tracking/Change tracking (new)/Manage consumer groups.md).

-   Reselect objects for change tracking

When the change tracking task is running, you can reselect the objects to be tracked. For more information, see [Modify objects for change tracking](/intl.en-US/Change Tracking/Change tracking (new)/Modify objects for change tracking.md).

-   View tracked data changes

You can use the DTS console to view the data changes that DTS has captured from the data source. For more information, see [View tracked data changes](/intl.en-US/Change Tracking/Change tracking (new)/View tracked data changes.md). |
|4. Consume tracked data|Use a Kafka client to consume tracked data. For more information, see [Use a Kafka client to consume tracked data](/intl.en-US/Change Tracking/Change tracking (new)/Use a Kafka client to consume tracked data.md).|
|5. Stop the change tracking task|If you want to stop the change tracking task, delete the instance.-   Pay-as-you-go: You can delete the change tracking instance that is no longer needed.
-   Subscription: A subscription change tracking instance cannot be manually deleted. It will be automatically deleted seven days after the expiration date.

**Note:** You can also reset a change tracking instance to stop the associated task. For more information, see [Reset a change tracking task](/intl.en-US/Change Tracking/Change tracking (new)/Reset a change tracking task.md). |

