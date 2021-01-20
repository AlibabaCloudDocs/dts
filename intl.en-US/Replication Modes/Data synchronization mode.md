# Data synchronization mode

The data synchronization mode of Data Transmission Service \(DTS\) replicates ongoing changes between two data stores. This mode is typically used for OLTP-to-OLAP replications and disaster recovery-related replications.

## Supported objects

Objects that can be replicated include databases, tables, and columns. You can specify objects that you want to replicate.

## Advanced features

|Feature|Description|References|
|-------|-----------|----------|
|Reselect objects to be synchronized|You can add and remove objects to be synchronized while a data synchronization task is running.|-   [Add an object to a data synchronization task](/intl.en-US/Data Synchronization/Synchronization task management/Add an object to a data synchronization task.md)
-   [Remove an object from a data synchronization task](/intl.en-US/Data Synchronization/Synchronization task management/Remove an object from a data synchronization task.md) |
|Track synchronization performance|You can check the performance of a data synchronization task while it is running.|[View the connection status and performance of data synchronization](/intl.en-US/Data Synchronization/Synchronization task management/View the connection status and performance of data synchronization.md)|
|ETL features|-   Object name mapping: You can define mappings to name target objects differently from their source objects. Supported objects include databases, tables, and columns.
-   Data filtering: You can use SQL-like queries to filter the source table to replicate only records that match certain conditions.

|-   [Specify the name of an object in the destination instance](/intl.en-US/Data Synchronization/Synchronization task management/Specify the name of an object in the destination instance.md)
-   [Use SQL conditions to filter data](/intl.en-US/Data Synchronization/Synchronization task management/Use SQL conditions to filter data.md) |

## Data synchronization task lifecycle

To fulfill a data replication process in any of the three modes, you must create a data replication task. A data replication task holds all the configurations of the replication process, such as the source database, target database, and objects to be replicated.

The following table describes the statuses of a synchronization task.

|Task status|Description|Applicable operations|
|-----------|-----------|---------------------|
|Prechecking|A precheck is being performed and the synchronization task has not started.|-   View the synchronization configurations
-   Delete the synchronization task
-   Duplicate synchronization configurations |
|Precheck Failed|The synchronization task failed to pass the precheck.|-   Perform the precheck
-   View the synchronization configurations
-   Reselect objects to be synchronized
-   Modify the synchronization speed
-   Delete the synchronization task
-   Duplicate synchronization configurations |
|Not Started|The synchronization task has passed the precheck but is not started.|-   Perform the precheck
-   Start the synchronization task
-   Reselect objects to be synchronized
-   Modify the synchronization speed
-   Delete the synchronization task
-   Duplicate synchronization configurations |
|Performing Initial Synchronization|The initial synchronization is in progress.|-   View the synchronization configurations
-   Delete the synchronization task
-   Duplicate synchronization configurations |
|Initial Synchronization Failed|Data replication has failed during initial synchronization.|-   View the synchronization configurations
-   Reselect objects to be synchronized
-   Modify the synchronization speed
-   Delete the synchronization task
-   Duplicate synchronization configurations |
|Synchronizing|The task is replicating data.|-   View the synchronization configurations
-   Reselect objects to be synchronized
-   Modify the synchronization speed
-   Delete the synchronization task
-   Duplicate synchronization configurations |
|Synchronization Failed|An error occurred during synchronization.|-   View the synchronization configurations
-   Reselect objects to be synchronized
-   Modify the synchronization speed
-   Delete the synchronization task
-   Duplicate synchronization configurations |
|Paused|The synchronization task is paused.|-   View the synchronization configurations
-   Reselect objects to be synchronized
-   Modify the synchronization speed
-   Delete the synchronization task
-   Duplicate synchronization configurations |

A synchronization task in the **Synchronization Failed** or **Initial Synchronization Failed** status will be automatically retried multiple times within seven days of the failure occurring. To discard the data synchronization, you must manually pause or stop the synchronization task.

