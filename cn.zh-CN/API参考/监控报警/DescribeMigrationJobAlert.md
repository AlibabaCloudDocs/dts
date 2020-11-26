# DescribeMigrationJobAlert

调用DescribeMigrationJobAlert接口查询迁移任务的监控报警设置详情。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=Dts&api=DescribeMigrationJobAlert&type=RPC&version=2020-01-01)

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|Action|String|是|DescribeMigrationJobAlert|系统规定参数，取值：**DescribeMigrationJobAlert**。 |
|MigrationJobId|String|是|dtslb9113qq11n\*\*\*\*|数据迁移实例ID，可以通过调用**DescribeMigrationJobs**接口查询。 |
|RegionId|String|否|cn-hangzhou|地域ID，传入本参数来指定实例所在地域，详情请参见[支持的地域列表](~141033~)。 |
|ClientToken|String|否|ETnLKlblzczshOTUbOCz\*\*\*\*|用于保证请求的幂等性。由客户端生成该参数值，要保证在不同请求间唯一，最大值不超过64个ASCII字符，且该参数值中不能包含非ASCII字符。 |
|AccountId|String|否|12323344\*\*\*\*|阿里云主账号ID，无需设置，该参数即将下线。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|DelayAlertPhone|String|1361234\*\*\*\*,1371234\*\*\*\*|延迟报警的联系人手机号码。 |
|DelayAlertStatus|String|enable|是否监控延迟状态，返回值：

 -   **enable**：是。
-   **disable**：否。 |
|ErrorAlertPhone|String|1361234\*\*\*\*,1371234\*\*\*\*|异常报警的联系人手机号码。 |
|ErrCode|String|InternalError|调用出错时返回的错误码。 |
|ErrMessage|String|The request processing has failed due to some unknown error.|调用出错时返回的错误信息。 |
|DelayOverSeconds|String|10|触发延迟报警的阈值，单位为秒。 |
|ErrorAlertStatus|String|enable|是否监控异常状态，返回值：

 -   **enable**：是。
-   **disable**：否。 |
|MigrationJobId|String|lb9113qq11n\*\*\*\*|数据迁移实例ID。 |
|MigrationJobName|String|PolarDB MySQL\_to\_RDS MySQL|数据迁移任务名称。 |
|RequestId|String|210ec21916055187057342533d\*\*\*\*|请求ID。 |
|Success|String|true|请求是否成功。 |

## 示例

请求示例

```
http(s)://dts.aliyuncs.com/?Action=DescribeMigrationJobAlert
&MigrationJobId=dtslb9113qq11n****
&<公共请求参数>
```

正常返回示例

`XML` 格式

```
<DescribeMigrationJobAlertresponse>
      <RequestId>210ec21916055187057342533d****</RequestId>
      <DelayAlertPhone>1361234****,1371234****</DelayAlertPhone>
      <ErrorAlertStatus>enable</ErrorAlertStatus>
      <MigrationJobName>PolarDB MySQL_to_RDS MySQL</MigrationJobName>
      <DelayAlertStatus>enable</DelayAlertStatus>
      <DelayOverSeconds>10</DelayOverSeconds>
      <Success>true</Success>
      <MigrationJobId>lb9113qq11n****</MigrationJobId>
</DescribeMigrationJobAlertresponse>
```

`JSON` 格式

```
{
	"RequestId": "210ec21916055187057342533d****",
	"DelayAlertPhone": "1361234****,1371234****",
    "ErrorAlertStatus": "enable",
	"MigrationJobName": "PolarDB MySQL_to_RDS MySQL",
	"DelayAlertStatus": "enable",
	"DelayOverSeconds": "10",
	"Success": true,
	"MigrationJobId": "lb9113qq11n****"
}
```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/Dts)查看更多错误码。

