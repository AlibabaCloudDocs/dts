# DescribeSynchronizationJobs

通过DescribeSynchronizationJobs查询数据同步实例列表和各同步实例的详情。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=Dts&api=DescribeSynchronizationJobs&type=RPC&version=2020-01-01)

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|Action|String|是|DescribeSynchronizationJobs|系统规定参数，取值：**DescribeSynchronizationJobs**。 |
|Tag.N.Key|String|是|testkey1|标签键。

 **说明：**

-   N表示传入第几个标签的键。例如：Tag.1.Key表示传入第一个标签的键；Tag.2.Key表示传入第二个标签的键。可以同时查询1~20个标签键。
-   该参数传入时不允许传入空字符串。 |
|RegionId|String|否|cn-hangzhou|地域ID，传入本参数来指定实例所在地域，详情请参见[支持的地域列表](~141033~)。 |
|PageSize|Integer|否|30|每页记录数，取值： **30**、**50**、**100**，默认值为**30**。 |
|PageNum|Integer|否|1|页码，取值为大于**0**且不超过Integer数据类型的的最大值，默认值为**1**。 |
|SynchronizationJobName|String|否|MySQL同步|同步作业名称，传入本参数后，DTS将在调用结果中返回包含该名称的同步作业。 |
|ClientToken|String|否|0c593ea1-3bea-11e9-b96b-88e9fe63\*\*\*\*|保证请求幂等性。从您的客户端生成一个参数值，确保不同请求间该参数值唯一。**ClientToken**只支持ASCII字符，且不能超过64个字符。 |
|AccountId|String|否|12323344\*\*\*\*|阿里云主账号ID，无需设置，该参数即将下线。 |
|Tag.N.Value|String|否|testvalue1|标签的值。

 **说明：**

-   N表示传入第几个标签的值。例如：Tag.1.Value表示传入第一个标签的值；Tag.2.Value表示传入第二个标签的值。可以同时查询1~20个标签值。
-   允许传入空字符串。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|PageNumber|Integer|1|页码。 |
|PageRecordCount|Integer|30|当前页可展示的最大记录数。 |
|RequestId|String|92E1E99D-5224-4AD3-8C94-23A3516B\*\*\*\*|请求ID。 |
|SynchronizationInstances|Array of SynchronizationInstance| |数据同步实例列表和各同步实例的详情。 |
|CreateTime|String|2021-01-11 11:39:44.0|同步实例创建时间，*yyyy-MM-dd*T*HH:mm:ss*Z（UTC时间）。 |
|DataInitialization|String|true|是否执行全量数据初始化，取值：

 -   **true**：是。
-   **false**：否。 |
|DataInitializationStatus|Struct| |全量数据初始化状态。 |
|ErrorMessage|String|java.lang.NumberFormatException: For input string: ""|全量数据初始化失败的错误提示。 |
|Percent|String|100|全量数据初始化进度，单位为百分比。 |
|Progress|String|200001|已完成全量数据初始化的记录数。 |
|Status|String|Finished|全量数据初始化状态，取值包括：

 -   **NotStarted**：未启动
-   **Migrating**：同步中
-   **Failed**：同步失败
-   **Finished**：同步完成 |
|DataSynchronizationStatus|Struct| |增量数据同步状态。 |
|Delay|String|0|增量数据同步延迟，单位为秒。 |
|ErrorMessage|String|任务失败太久 无法恢复|增量数据同步失败的错误提示。 |
|Percent|String|100|增量数据同步进度，单位为百分比。 |
|Status|String|Finished|增量数据迁移状态，取值包括：

 **NotStarted**：未启动

 -   **Migrating**: 迁移中
-   **Failed**:迁移失败
-   **Finished**: 迁移完成 |
|Delay|String|0|同步延迟，单位为秒。 |
|DestinationEndpoint|Struct| |目标实例的连接信息。 |
|EngineName|String|MySQL|目标库的数据库类型。 |
|IP|String|172.16.88.\*\*\*|目标库的连接地址。 |
|InstanceId|String|rm-bp162d4tp0500\*\*\*\*|目标库的实例ID。 |
|InstanceType|String|RDS|目标库的实例类型。 |
|Port|String|3306|目标库的数据库服务端口。 |
|UserName|String|dtstest|目标库的数据库账号。 |
|ErrorMessage|String|DTS-070211: Connect Source DB failed. cause by \[com.mysql.jdbc.exceptions.jdbc4.MySQLNonTransientConnectionException:Could not create connection to database server. Attempted reconnect 3 times. Giving up.\]\[com.mysql.jdbc.exceptions.jdbc4.CommunicationsException:Communications link failure\\n\\nThe last packet sent successfully to the server was 0 milliseconds ago. The driver has not received any packets from the server.\]\[java.net.ConnectException:Connection timed out \(Connection timed out\)\] About more information in \[https://yq.aliyun.com/articles/499178\].|数据同步失败的错误提示。 |
|ExpireTime|String|2021-03-07T16:00:00Z|同步作业的到期时间，*yyyy-MM-dd*T*HH:mm:ss*Z（UTC时间）。

 **说明：** 当**PayType**参数的返回值为**PrePaid**时才会返回本参数。 |
|PayType|String|PrePaid|同步作业的付费类型，返回值：

 -   **PrePaid**：包年包月（预付费）。
-   **PostPaid**：按量付费（后付费）。 |
|Performance|Struct| |同步链路概况信息。 |
|FLOW|String|1|每秒同步数据的流量，单位为MB/s。 |
|RPS|String|100|每秒同步SQL语句的次数，包括BEGIN、COMMIT 、DML语句（INSERT、DELETE、UPDATE）及DDL语句。 |
|PrecheckStatus|Struct| |预检查状态。 |
|Detail|Array of CheckItem| |各预检查项的执行情况。 |
|CheckStatus|String|Success|检查结果，返回值：

 -   **Success**：通过该项检查。
-   **Failed**：未通过该项检查。 |
|ErrorMessage|String|Original error: Access denied for user 'dtstest'@'100.104.\*\*\*.\*\*' \(using password: YES\)|预检查未通过的错误提示。

 **说明：** 当**CheckStatus**参数返回值为**Failed**时，才会返回本参数。 |
|ItemName|String|CHECK\_CONN\_SRC|预检查项目。 |
|RepairMethod|String|CHECK\_ERROR\_DEST\_CONN\_REPAIR2|预检查未通过时对应的修复方法。

 **说明：** 当**CheckStatus**参数返回值为**Failed**时，才会返回本参数。 |
|Percent|String|100|预检查整体进度，单位为百分比。 |
|Status|String|Success|预检查结果，返回值：

 -   **Success**：通过该项检查。
-   **Failed**：未通过该项检查。 |
|SourceEndpoint|Struct| |源实例连接信息。 |
|EngineName|String|MySQL|源库的数据库类型。 |
|IP|String|172.16.88.\*\*\*|源库的连接地址。 |
|InstanceId|String|rm-bp1i99e8l7913\*\*\*\*|源库的实例ID。 |
|InstanceType|String|RDS|源库的实例类型。 |
|Port|String|3306|源库的数据库服务端口。 |
|UserName|String|dtstest|源库的数据库账号。 |
|Status|String|synchronizing|同步实例的状态，返回值：

 -   **NotStarted**：未启动。
-   **Prechecking**：预检查中。
-   **PrecheckFailed**：预检查失败。
-   **Initializating**：同步初始化。
-   **InitializeFailed**：同步初始化失败。
-   **Synchronizing**：同步中。
-   **Failed**：同步失败。
-   **Suspending**：暂停。
-   **Modifying**：同步对象变更中。
-   **Finished**：完成。 |
|StructureInitialization|String|true|是否执行结构初始化，返回值：

 -   **true**：是
-   **false**：否 |
|StructureInitializationStatus|Struct| |结构初始化状态。 |
|ErrorMessage|String|DTS-1020042 Execute sql error sql: ERROR: type "geometry" does not exist;|结构初始化失败的错误提示。 |
|Percent|String|100|结构初始化进度，单位为百分比。 |
|Progress|String|1|已完成结构初始化的表的数量。 |
|Status|String|Finished|结构初始化状态，取值包括：

 -   **NotStarted**：未启动
-   **Migrating**: 迁移中
-   **Failed**:迁移失败
-   **Finished**: 迁移完成 |
|SynchronizationDirection|String|Forward|同步方向，返回值：

 -   **Forward**：正向。
-   **Reverse**：反向。 |
|SynchronizationJobClass|String|large|同步链路规格。 |
|SynchronizationJobId|String|dtsexjk1alb116\*\*\*\*|数据同步实例ID。 |
|SynchronizationJobName|String|MySQL同步|同步作业名称。 |
|SynchronizationObjects|Array of SynchronizationObject| |同步对象。 |
|NewSchemaName|String|newdtstestdatabase|待同步的库在目标数据库中映射的名称。 |
|SchemaName|String|dtstestdatabase|待同步的库名。 |
|TableExcludes|Array of TableExclude| |待同步库中排除的表，这些表将不会被同步。 |
|TableName|String|order|排除的表的名称。 |
|TableIncludes|Array of TableInclude| |待同步的表。 |
|TableName|String|customer|待同步表名。 |
|Tags|Array of Tag| |标签集合。 |
|Key|String|testkey1|标签键。 |
|Value|String|testvalue1|标签键对应的标签值。 |
|TotalRecordCount|Long|100|阿里云主账号下所有符合条件的数据同步实例数量。 |

## 示例

请求示例

```
http(s)://dts.aliyuncs.com/?Action=DescribeSynchronizationJobs
&<公共请求参数>
```

正常返回示例

`XML`格式

```
<DescribeSynchronizationJobsResponse>
      <TotalRecordCount>100</TotalRecordCount>
      <SynchronizationInstances>
            <Status>synchronizing</Status>
            <SynchronizationObjects>
                  <SchemaName>dtstestdatabase</SchemaName>
                  <NewSchemaName>newdtstestdatabase</NewSchemaName>
            </SynchronizationObjects>
            <DataInitializationStatus>
                  <Status>Finished</Status>
                  <Progress>200001</Progress>
                  <Percent>100</Percent>
            </DataInitializationStatus>
            <SynchronizationJobName>MySQL同步</SynchronizationJobName>
            <CreateTime>2021-01-11 11:39:44.0</CreateTime>
            <PayType>PostPaid</PayType>
            <Delay>0</Delay>
            <PrecheckStatus>
                  <Status>Finished</Status>
                  <Percent>100</Percent>
                  <Detail>
                        <CheckStatus>Success</CheckStatus>
                        <ItemName>CHECK_CONN_SRC</ItemName>
                  </Detail>
                  <Detail>
                        <CheckStatus>Success</CheckStatus>
                        <ItemName>CHECK_AUTH_SRC</ItemName>
                  </Detail>
                  <Detail>
                        <CheckStatus>Success</CheckStatus>
                        <ItemName>CHECK_CONN_DEST</ItemName>
                  </Detail>
                  <Detail>
                        <CheckStatus>Success</CheckStatus>
                        <ItemName>CHECK_AUTH_DEST</ItemName>
                  </Detail>
                  <Detail>
                        <CheckStatus>Success</CheckStatus>
                        <ItemName>CHECK_BINLOG_ON</ItemName>
                  </Detail>
                  <Detail>
                        <CheckStatus>Success</CheckStatus>
                        <ItemName>CHECK_BINLOG_MODE</ItemName>
                  </Detail>
                  <Detail>
                        <CheckStatus>Success</CheckStatus>
                        <ItemName>CHECK_BINLOG_ROW_IMAGE</ItemName>
                  </Detail>
                  <Detail>
                        <CheckStatus>Success</CheckStatus>
                        <ItemName>CHECK_SERVER_ID</ItemName>
                  </Detail>
                  <Detail>
                        <CheckStatus>Success</CheckStatus>
                        <ItemName>CHECK_BINLOG_EXIST</ItemName>
                  </Detail>
                  <Detail>
                        <CheckStatus>Success</CheckStatus>
                        <ItemName>CHECK_ENGINE</ItemName>
                  </Detail>
                  <Detail>
                        <CheckStatus>Success</CheckStatus>
                        <ItemName>CHECK_MYSQL_PASSOWRD_FORMAT</ItemName>
                  </Detail>
                  <Detail>
                        <CheckStatus>Success</CheckStatus>
                        <ItemName>CHECK_SRC</ItemName>
                  </Detail>
                  <Detail>
                        <CheckStatus>Success</CheckStatus>
                        <ItemName>CHECK_SAME_OBJ</ItemName>
                  </Detail>
                  <Detail>
                        <CheckStatus>Success</CheckStatus>
                        <ItemName>CHECK_SQL_MODE</ItemName>
                  </Detail>
                  <Detail>
                        <CheckStatus>Success</CheckStatus>
                        <ItemName>CHECK_SAME_USER</ItemName>
                  </Detail>
                  <Detail>
                        <CheckStatus>Success</CheckStatus>
                        <ItemName>CHECK_TOPOLOGY</ItemName>
                  </Detail>
            </PrecheckStatus>
            <SynchronizationJobId>dtsexjk1alb116lbf1</SynchronizationJobId>
            <DataInitialization>true</DataInitialization>
            <DestinationEndpoint>
                  <InstanceId>rm-bp162d4tp0500****</InstanceId>
                  <InstanceType>RDS</InstanceType>
                  <EngineName>MySQL</EngineName>
            </DestinationEndpoint>
            <SourceEndpoint>
                  <InstanceId>rm-bp1i99e8l7913****</InstanceId>
                  <InstanceType>RDS</InstanceType>
                  <EngineName>MySQL</EngineName>
            </SourceEndpoint>
            <StructureInitialization>true</StructureInitialization>
            <ExpireTime>2021-03-07T16:00:00Z</ExpireTime>
            <Performance>
                  <RPS>0</RPS>
                  <FLOW>0.00MB/s</FLOW>
            </Performance>
            <SynchronizationJobClass>large</SynchronizationJobClass>
            <DataSynchronizationStatus></DataSynchronizationStatus>
            <StructureInitializationStatus>
                  <Status>Finished</Status>
                  <Progress>1</Progress>
                  <Percent>100</Percent>
            </StructureInitializationStatus>
      </SynchronizationInstances>
      <PageRecordCount>30</PageRecordCount>
      <RequestId>92E1E99D-5224-4AD3-8C94-23A3516B****</RequestId>
      <PageNumber>1</PageNumber>
</DescribeSynchronizationJobsResponse>
```

`JSON`格式

```
{
	"TotalRecordCount": 100,
	"SynchronizationInstances": [
		{
			"Status": "synchronizing",
			"SynchronizationObjects": [
				{
					"TableIncludes": [],
					"TableExcludes": [],
					"SchemaName": "dtstestdatabase",
					"NewSchemaName": "newdtstestdatabase"
				}
			],
			"DataInitializationStatus": {
				"Status": "Finished",
				"Progress": "200001",
				"Percent": "100"
			},
			"SynchronizationJobName": "MySQL同步",
			"CreateTime": "2021-01-11 11:39:44.0",
			"PayType": "PostPaid",
			"Delay": "0",
			"PrecheckStatus": {
				"Status": "Finished",
				"Percent": "100",
				"Detail": [
					{
						"CheckStatus": "Success",
						"ItemName": "CHECK_CONN_SRC"
					},
					{
						"CheckStatus": "Success",
						"ItemName": "CHECK_AUTH_SRC"
					},
					{
						"CheckStatus": "Success",
						"ItemName": "CHECK_CONN_DEST"
					},
					{
						"CheckStatus": "Success",
						"ItemName": "CHECK_AUTH_DEST"
					},
					{
						"CheckStatus": "Success",
						"ItemName": "CHECK_BINLOG_ON"
					},
					{
						"CheckStatus": "Success",
						"ItemName": "CHECK_BINLOG_MODE"
					},
					{
						"CheckStatus": "Success",
						"ItemName": "CHECK_BINLOG_ROW_IMAGE"
					},
					{
						"CheckStatus": "Success",
						"ItemName": "CHECK_SERVER_ID"
					},
					{
						"CheckStatus": "Success",
						"ItemName": "CHECK_BINLOG_EXIST"
					},
					{
						"CheckStatus": "Success",
						"ItemName": "CHECK_ENGINE"
					},
					{
						"CheckStatus": "Success",
						"ItemName": "CHECK_MYSQL_PASSOWRD_FORMAT"
					},
					{
						"CheckStatus": "Success",
						"ItemName": "CHECK_SRC"
					},
					{
						"CheckStatus": "Success",
						"ItemName": "CHECK_SAME_OBJ"
					},
					{
						"CheckStatus": "Success",
						"ItemName": "CHECK_SQL_MODE"
					},
					{
						"CheckStatus": "Success",
						"ItemName": "CHECK_SAME_USER"
					},
					{
						"CheckStatus": "Success",
						"ItemName": "CHECK_TOPOLOGY"
					}
				]
			},
			"SynchronizationJobId": "dtsexjk1alb116lbf1",
			"DataInitialization": true,
			"DestinationEndpoint": {
				"InstanceId": "rm-bp162d4tp0500****",
				"InstanceType": "RDS",
				"EngineName": "MySQL"
			},
			"SourceEndpoint": {
				"InstanceId": "rm-bp1i99e8l7913****",
				"InstanceType": "RDS",
				"EngineName": "MySQL"
			},
			"StructureInitialization": true,
			"ExpireTime": "2021-03-07T16:00:00Z",
            "Performance": {
				"RPS": "0",
				"FLOW": "0.00MB/s"
			},
			"SynchronizationJobClass": "large",
			"Tags": [],
			"DataSynchronizationStatus": {},
			"StructureInitializationStatus": {
				"Status": "Finished",
				"Progress": "1",
				"Percent": "100"
			}
		}
	],
	"PageRecordCount": 30,
	"RequestId": "92E1E99D-5224-4AD3-8C94-23A3516B****",
	"PageNumber": 1
}
```

## 错误码

访问[错误中心](https://error-center.alibabacloud.com/status/product/Dts)查看更多错误码。

