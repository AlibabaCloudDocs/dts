# Create a data synchronization task

You can follow the general procedure in this topic to create and manage a task in data synchronization mode.

## Background information

-   Use cases

    The data synchronization mode of DTS replicates ongoing changes between two data stores. This mode is typically used for OLTP-to-OLAP replications and disaster recovery-related replications.

-   Supported sources and targets

    For more information about supported data sources and targets, see [Data synchronization](/intl.en-US/Introduction/Supported sources and targets.md).

-   Data type conversion

    Different database engines use different sets of data types. To check the data type conversion that DTS applies between the source and target databases during initial schema synchronization, see [Data type conversion](https://www.alibabacloud.com/help/doc-detail/177111.htm).

-   Advanced features

    For information about the features of a data synchronization task, see [Data synchronization mode](/intl.en-US/Replication Modes/Data synchronization mode.md).


## Data synchronization procedure

You can complete the following higher level steps to create and manage a data synchronization task.

|Step|Description|
|----|-----------|
|1. Set up the networking environment|Before you use DTS for data synchronization, prepare the environments that are required for the source and target databases.

For more information, see [Set up a network environment for replication](/intl.en-US/Network Setup/Set up a network environment for replication.md). |
|2. Create a data synchronization instance and task|Create a data synchronization task in the DTS console. For detailed, step-by-step procedures about specific synchronization scenarios, see [Overview of data synchronization scenarios](/intl.en-US/Data Synchronization/Overview of data synchronization scenarios.md).|
|3. Monitor the performance of the synchronization task and manage objects|View the performance metrics of the task. For more information, see [View the connection status and performance of data synchronization](/intl.en-US/Data Synchronization/Synchronization task management/View the connection status and performance of data synchronization.md).

You can also add objects to or remove objects from the task. For more information, see [Add an object to a data synchronization task](/intl.en-US/Data Synchronization/Synchronization task management/Add an object to a data synchronization task.md) and [Remove an object from a data synchronization task](/intl.en-US/Data Synchronization/Synchronization task management/Remove an object from a data synchronization task.md). |
|4. Stop the data synchronization task|Stop the data synchronization task if you no longer need the task. For more information, see [Stop a data synchronization task](/intl.en-US/Data Synchronization/Synchronization task management/Stop a data synchronization task.md).|

