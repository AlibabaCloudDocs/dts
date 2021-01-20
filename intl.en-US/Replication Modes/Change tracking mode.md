# Change tracking mode

The change tracking replication mode of Data Transmission Service \(DTS\) captures data updates made to your source data store and exposes them as a publisher/subscriber stream. The source data store can be either a user-created MySQL database or an ApsaraDB for MySQL database. The change tracking mode allows you to build a distributed application with decoupled data systems, so you can implement your asynchronous replications between these data systems.

**Note:** The change tracking mode supersedes the former "data subscription" mode. The change tracking mode is more cost-effective than its predecessor in that it supports consumption by multiple consumer groups in the same instance.

## Features

-   You can allow multiple consumers to subscribe to the same source. For more information, see [Manage consumer groups](/intl.en-US/Change Tracking/Change tracking (new)/Manage consumer groups.md).
-   You can track changes that are made to specific data tables.
-   You can track data changes from user-created MySQL databases.
-   You can expose the stream to consumer applications either located in the classic network or in a VPC. The VPC access mode provides lower latency and higher security.

    **Note:** You can select a network type when you create a change tracking task. For more information, see [Track data changes from ApsaraDB RDS for MySQL \(new\)](/intl.en-US/Change Tracking/Change tracking (new)/Track data changes from ApsaraDB RDS for MySQL (new).md).

-   You can consume data in a change tracking task by using a standard Kafka client.

    **Note:** Only Kafka versions 0.10.0.x to 1.1.x are supported. For more information, see [Use a Kafka client to consume tracked data](/intl.en-US/Change Tracking/Change tracking (new)/Use a Kafka client to consume tracked data.md).

-   You can reselect objects for change tracking. For more information, see [Modify objects for change tracking](/intl.en-US/Change Tracking/Change tracking (new)/Modify objects for change tracking.md).
-   You can modify consumption checkpoints.

    **Note:** You must use your change tracking client to modify consumption checkpoints.

-   You can monitor the status of the change tracking task. You can set a threshold for the consumer latency, depending on how sensitive your application is to latency. You receive an alert when the threshold is reached.

## Consumer groups

You can create consumer groups to allow multiple consumer applications to subscribe to the same change tracking task independently. Therefore, applications in different consumer groups can consume the same update more than once.

**Note:** Currently, each consumer group can include only one consumer.

## Supported clients

You can consume data updates by using a Kafka client, which can be coded in your preferred programming language. For more information, see the Kafka clients documentation available on the Apache Kafka website. DTS provides an example of using a Kafka client coded in Java to consume data updates. For more information, see [Use a Kafka client to consume tracked data](/intl.en-US/Change Tracking/Change tracking (new)/Use a Kafka client to consume tracked data.md).

**Note:** If you use the official Kafka client to consume data, you must specify the username in the following format: <Consumer group account\>-<Consumer group ID\>, for example, dtstest-dtsae\*\*\*\*\*\*bpv. Otherwise, the connection fails.

## Supported data change types and objects

The change tracking mode supports the following types of updates:

-   Schema updates:

    Schema updates are also known as data definition language \(DDL\) changes. You can track operations that create, delete, or modify the schema objects in database instances. In your application code, you need to implement filtering on the data updates to identify schema updates.

-   Data updates:

    Data updates are also known as data manipulation language \(DML\) changes. You can track data updates of a selected object, including the INSERT, DELETE, and UPDATE operations.


You can enable change tracking for the following types of objects:

-   Databases
-   Tables

## Change tracking tasks

A change tracking task pulls data updates from the source database in real time and keeps the updates for 24 hours. You can use a Kafka client to consume the data updates captured by the task. You can create, manage, and delete change tracking channels in the DTS console.

## Change tracking task lifecycle

The following table describes the statuses of a change tracking task:

|Task status|Description|Applicable operations|
|-----------|-----------|---------------------|
|Prechecking|The change tracking task has been configured and a precheck is in process.|-   Delete the change tracking instance |
|Not Started|The change tracking channel has passed the precheck, but is not started.|-   Delete the change tracking instance
-   Reselect objects for change tracking |
|Performing Initial Change Tracking|The initial change tracking is in progress. This process takes about 1 minute.|-   Delete the change tracking instance
-   Reselect objects for change tracking |
|Normal|Data updates are being pulled from the data source.|-   View sample code
-   Configure monitoring and alerts
-   Delete the change tracking instance
-   Reset the change tracking instance
-   Reselect objects for change tracking |
|Error|An error occurs when the change tracking task is pulling data updates from thedata source.|-   View sample code
-   Configure monitoring and alerts
-   Delete the change tracking instance
-   Reset the change tracking instance
-   Reselect objects for change tracking |

**Note:**

-   A change tracking task in the **Error** status will automatically retry within seven days of the failure. To discard the change tracking progress and configurations, you must delete the change tracking instance.
-   After a change tracking instance is released, all the instance and task configurations are permanently cleared. Use caution when you perform this operation.

