# Use a Kafka client to consume tracked data

This topic describes how to use the demo code of a Kafka client to consume tracked data. The change tracking feature of the new version allows you to consume tracked data by using a Kafka client \(V0.11 to V2.0\).

## Precautions

-   If you enable auto commit when you use the change tracking feature, some data may be committed before it is consumed. This results in data loss. We recommend that you manually commit data.

    **Note:** If data fails to be committed due to a fault, you can restart the client to continue consuming data from the last recorded consumer offset. However, duplicate data may be generated during this period. You must manually filter out the duplicate data.

-   Data is serialized and stored in the Avro format. For more information, see [Record.avsc](https://github.com/LioRoger/subscribe_example/blob/master/avro/Record.avsc).

    **Warning:** If you are not using the Kafka client that is described in this topic, you must parse the tracked data based on the Avro schema.

-   The search unit is second when DTS calls the `offsetFotTimes` operation. The search unit is millisecond when a native Kafka client calls this operation.

## Run the Kafka client

Click [here](https://github.com/LioRoger/subscribe_example) to download the demo code of the Kafka client. For more information about how to use the demo code, see [Readme](https://github.com/LioRoger/subscribe_example/blob/master/javaimpl/Readme).

**Note:** If you use a Kafka client of version 2.0, you must change the version number in the subscribe\_example-master/javaimpl/pom.xml file to 2.0.0.

![kafka2.0](../images/p171738.png)

|Step|File or directory|
|----|-----------------|
|1. Use the native Kafka consumer to obtain incremental data from the change tracking instance.|subscribe\_example-master/javaimpl/src/main/java/recordgenerator/|
|2. Deserialize the image of the incremental data, and obtain , and other attributes.|subscribe\_example-master/javaimpl/src/main/java/boot/MysqlRecordPrinter.java|
|3. Convert the dataTypeNumber values in the deserialized data into MySQL data types. **Note:** For more information, see [Mappings between MySQL data types and dataTypeNumber values](#section_woc_4pq_mes).

|subscribe\_example-master/javaimpl/src/main/java/recordprocessor/mysql/|

## Procedure

The following steps show how to run the Kafka client to consume tracked data. IntelliJ IDEA \(Community Edition 2018.1.4 Windows\) is used in this example.****

1.  Create a change tracking task. For more information, see [Track data changes from an ApsaraDB RDS for MySQL instance \(new\)](/intl.en-US/Change Tracking/Change tracking (new)/Track data changes from an ApsaraDB RDS for MySQL instance (new).md).

2.  Create one or more consumer groups. For more information, see [Create consumer groups](/intl.en-US/Change Tracking/Change tracking (new)/Create consumer groups.md).

3.  Download the [demo code of the Kafka client](https://github.com/LioRoger/subscribe_example) and decompress the package.

4.  Open IntelliJ IDEA. In the window that appears, click **Open**.

    ![Open a project](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2583097951/p88391.png)

5.  In the dialog box that appears, go to the directory where the downloaded demo code resides. Find the pom.xml file.

    ![Open the pom.xml file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2583097951/p88398.png)

6.  In the dialog box that appears, select **Open as Project**.

7.  On the IntelliJ IDEA page, expand folders to find the demo file of the Kafka client, and double-click the file. The file name is NotifyDemo.java.

    ![Open the demo file of the Kafka client](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6308539951/p88407.png)

8.  Set the parameters in the NotifyDemo.java file.

    ![Set parameters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2583097951/p88411.png)

    |Parameter|Description|Method to obtain|
    |:--------|:----------|----------------|
    |USER\_NAME|The username of the consumer group. **Warning:** If you are not using the Kafka client that is described in this topic, you must specify the username in the following format: `<Consumer group account>-<Consumer group ID>`, for example, `dtstest-dtsae******bpv`. Otherwise, the connection fails.

|In the DTS console, click the instance ID, and then click Data Consume. You can obtain the **Consumer Group ID** and the corresponding **Account** information. **Note:** The password of the consumer group account is automatically specified when you create a consumer group.

![View the consumer group ID and account](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2583097951/p88419.png) |
    |PASSWORD\_NAME|The password of the account.|
    |SID\_NAME|The ID of the consumer group.|
    |GROUP\_NAME|The name of the consumer group. Set this parameter to the consumer group ID.|
    |KAFKA\_TOPIC|The topic of the change tracking instance.|In the DTS console, click the instance ID. On the Track Data Changes page, you can obtain the **tracked topic**, network address, and port number.![Obtain the topic and network information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2583097951/p48804.png) |
    |KAFKA\_BROKER\_URL\_NAME|The network address and port number of the change tracking instance. **Note:** If you track data changes over internal networks, the network latency is minimal. This is applicable if the ECS instance where you deploy the Kafka client belongs to the same VPC or classic network as the change tracking instance. |
    |INITIAL\_CHECKPOINT\_NAME|The consumer offset of consumed data. The value is a UNIX timestamp, for example, 1592269238. **Note:** You must save the consumer offset.

    -   If the consumption process is interrupted, you can specify the consumer offset on the change tracking client to resume data consumption. This allows you to prevent against data loss.
    -   When you start the change tracking client, you can specify the consumer offset to consume data on demand.
|The consumer offset of consumed data must be within the data range of the change tracking instance, as shown in the following figure. The consumer offset must be converted into a UNIX timestamp.![Data range](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8150013061/p127325.png)

**Note:**

    -   For more information about how to view the data range, see [View tracked data changes](/intl.en-US/Change Tracking/Change tracking (new)/View tracked data changes.md).
    -   You can use a search engine to obtain a UNIX timestamp converter. |
    |USE\_CONFIG\_CHECKPOINT\_NAME|Default value: true. The default value indicates that the client is forced to consume data from the specified consumer offset. This allows you to retain the data that is received but not processed.|N/A|
    |SUBSCRIBE\_MODE\_NAME|You can run two Kafka clients for a consumer group to implement disaster recovery. To use this feature, deploy two Kafka clients and set the value of the SUBSCRIBE\_MODE\_NAME parameter to subscribe.Default value: assign. The default value indicates that only one Kafka client is deployed.

|N/A|

9.  On the top of the IntelliJ IDEA page, choose **Run** \> **Run** to run the client.

    **Note:** When you run IntelliJ IDEA for the first time, it requires some time to load and install the relevant dependency.


## Running result of the Kafka client

The following figure shows that the Kafka client can track data changes from the source database.

![Running result of the Kafka client](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6308539951/p88599.png)

You can delete the // characters from the `//log.info(ret);` string in line 25 of the `NotifyDemo.java` file. Then, run the client again to view the data change information.

![Running details of the Kafka client](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6308539951/p88601.png)

## FAQ

-   Q: Why do I need to record the consumer offset of the Kafka client?

    A: The consumer offset recorded by DTS is the time when DTS receives a commit operation from the Kafka client. The recorded consumer offset may be different from the actual consumption time. If a business application or the Kafka client is unexpectedly interrupted, you can specify an accurate consumer offset to continue data consumption. This prevents against data loss or duplicate data consumption.


## Mappings between MySQL data types and dataTypeNumber values

|MySQL data type|Value of dataTypeNumber|
|:--------------|:----------------------|
|MYSQL\_TYPE\_DECIMAL|0|
|MYSQL\_TYPE\_INT8|1|
|MYSQL\_TYPE\_INT16|2|
|MYSQL\_TYPE\_INT32|3|
|MYSQL\_TYPE\_FLOAT|4|
|MYSQL\_TYPE\_DOUBLE|5|
|MYSQL\_TYPE\_NULL|6|
|MYSQL\_TYPE\_TIMESTAMP|7|
|MYSQL\_TYPE\_INT64|8|
|MYSQL\_TYPE\_INT24|9|
|MYSQL\_TYPE\_DATE|10|
|MYSQL\_TYPE\_TIME|11|
|MYSQL\_TYPE\_DATETIME|12|
|MYSQL\_TYPE\_YEAR|13|
|MYSQL\_TYPE\_DATE\_NEW|14|
|MYSQL\_TYPE\_VARCHAR|15|
|MYSQL\_TYPE\_BIT|16|
|MYSQL\_TYPE\_TIMESTAMP\_NEW|17|
|MYSQL\_TYPE\_DATETIME\_NEW|18|
|MYSQL\_TYPE\_TIME\_NEW|19|
|MYSQL\_TYPE\_JSON|245|
|MYSQL\_TYPE\_DECIMAL\_NEW|246|
|MYSQL\_TYPE\_ENUM|247|
|MYSQL\_TYPE\_SET|248|
|MYSQL\_TYPE\_TINY\_BLOB|249|
|MYSQL\_TYPE\_MEDIUM\_BLOB|250|
|MYSQL\_TYPE\_LONG\_BLOB|251|
|MYSQL\_TYPE\_BLOB|252|
|MYSQL\_TYPE\_VAR\_STRING|253|
|MYSQL\_TYPE\_STRING|254|
|MYSQL\_TYPE\_GEOMETRY|255|

