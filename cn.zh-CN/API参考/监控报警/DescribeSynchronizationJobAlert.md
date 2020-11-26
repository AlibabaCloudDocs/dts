# DescribeSynchronizationJobAlert

调用DescribeSubscriptionInstanceAlert接口查询同步作业的监控报警设置详情。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=Dts&api=DescribeSynchronizationJobAlert&type=RPC&version=2020-01-01)

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|Action|String|是|DescribeSynchronizationJobAlert|系统规定参数，取值：**DescribeSynchronizationJobAlert**。 |
|SynchronizationJobId|String|是|dtskxz1170c10p\*\*\*\*|数据同步实例ID，可以通过调用**DescribeSynchronizationJobs**接口查询。 |
|RegionId|String|否|cn-hangzhou|地域ID，传入本参数来指定订阅通道所在地域，详情请参见[支持的地域列表](~141033~)。 |
|SynchronizationDirection|String|否|Forward|同步方向，取值：

 -   **Forward**：正向。
-   **Reverse**：反向。

 **说明：** 默认取值为**Forward**。 |
|ClientToken|String|否|ETnLKlblzczshOTUbOCz\*\*\*\*|用于保证请求的幂等性。由客户端生成该参数值，要保证在不同请求间唯一，最大值不超过64个ASCII字符，且该参数值中不能包含非ASCII字符。 |
|AccountId|String|否|12323344\*\*\*\*|阿里云主账号ID，无需设置，该参数即将下线。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|DelayAlertPhone|String|1361234\*\*\*\*,1371234\*\*\*\*|延迟报警的联系人手机号码。 |
|DelayAlertStatus|String|enable|是否监控延迟状态，返回值：

 -   **enable**：是。
-   **disable**：否。 |
|DelayOverSeconds|String|10|触发延迟报警的阈值，单位为秒。 |
|ErrCode|String|InternalError|调用出错时返回的错误码。 |
|ErrMessage|String|The request processing has failed due to some unknown error.|调用出错时返回的错误信息。 |
|ErrorAlertPhone|String|1361234\*\*\*\*,1371234\*\*\*\*|异常报警的联系人手机号码。 |
|ErrorAlertStatus|String|enable|是否监控异常状态，返回值：

 -   **enable**：是。
-   **disable**：否。 |
|RequestId|String|210ec20e16055205968635339d\*\*\*\*|请求ID。 |
|Success|String|true|请求是否成功。 |
|SynchronizationDirection|String|Forward|同步方向，返回值：

 -   **Forward**：正向。
-   **Reverse**：反向。

**说明：** 只有在数据同步的拓扑为双向同步时，才会返回该参数。 |
|SynchronizationJobId|String|kxz1170c10p\*\*\*\*|数据同步实例ID。 |
|SynchronizationJobName|String|Polar MySQL\_TO\_RDS MySQL|数据同步作业名称。 |

## 示例

请求示例

```
http(s)://dts.aliyuncs.com/?Action=DescribeSynchronizationJobAlert
&SynchronizationJobId=dtskxz1170c10p****
&<公共请求参数>
```

正常返回示例

`XML` 格式

```
<DescribeSynchronizationJobAlertresponse>
      <SynchronizationJobId>kxz1170c10p****</SynchronizationJobId>
      <RequestId>210ec20e16055205968635339d****</RequestId>
      <SynchronizationJobName>Polar MySQL_TO_RDS MySQL</SynchronizationJobName>
      <DelayAlertPhone>1361234****,1371234****</DelayAlertPhone>
      <ErrorAlertStatus>enable</ErrorAlertStatus>
      <DelayAlertStatus>enable</DelayAlertStatus>
      <DelayOverSeconds>10</DelayOverSeconds>
      <Success>true</Success>
</DescribeSynchronizationJobAlertresponse>
```

`JSON` 格式

```
{
	"SynchronizationJobId": "kxz1170c10p****",
	"RequestId": "210ec20e16055205968635339d****",
	"SynchronizationJobName": "Polar MySQL_TO_RDS MySQL",
    "DelayAlertPhone": "1361234****,1371234****",
	"ErrorAlertStatus": "enable",
	"DelayAlertStatus": "enable",
	"DelayOverSeconds": "10",
	"Success": true
}
```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/Dts)查看更多错误码。

