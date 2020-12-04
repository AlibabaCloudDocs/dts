# Create a data migration task

You can follow the general procedure in this topic to create and manage a task in data migration mode.

## Background information

-   Use cases

    Typical scenarios include data migration to Alibaba Cloud from an on-premises system, migration between any two cloud providers, and data migration between instances within Alibaba Cloud. For more information, see [Use cases](/intl.en-US/Introduction/Use cases.md).

-   Migration paths

    For more information about supported data sources and targets, see [Data migration](/intl.en-US/Introduction/Supported sources and targets.md).

-   Data type conversion

    Different database engines use different sets of data types. You can check the data type conversion that DTS applies between the source and target databases during schema migration. For more information, see [Data type mappings between heterogeneous databases](/intl.en-US/Data Migration/Data type mappings between heterogeneous databases.md).

-   Advanced features

    For more information about the features of a data migration task, see [Data migration mode](/intl.en-US/Replication Modes/Data migration mode.md).


## Data migration procedure

You can complete the following higher level steps to create and manage a data migration task.

|Step|Description|
|----|-----------|
|1. Set up a network environment|Before you use DTS for data migration, you must make your source or target database reachable by DTS servers over the network.

For more information, [Set up a network environment for replication](/intl.en-US/Network Setup/Set up a network environment for replication.md) |
|2. Create a data migration task|Create a data migration task in the DTS console. For detailed, step-by-step procedures about specific migration scenarios, see [Overview of data migration scenarios](/intl.en-US/Data Migration/Overview of data migration scenarios.md). |
|3. View the progress of a data migration task|View the progress of schema migration, full data migration, or incremental data migration in the DTS console.

For more information, see [View the progress of a data migration task](/intl.en-US/Data Migration/Migration task management/View the progress of a data migration task.md). |
|4. Switch applications to the target database|Switch your applications to the target database. In this step you can also prepare a rollback solution to minimize the disruption caused by any error to your business operations.

For more information, see [Switch workloads to the destination database](/intl.en-US/Data Migration/Migration task management/Switch workloads to the destination database.md). |
|5. Stop the data migration task|After data migration and switchover is complete, stop the data migration task if you no longer need the task.

For more information, see [Stop a data migration task](/intl.en-US/Data Migration/Migration task management/Stop a data migration task.md). |

