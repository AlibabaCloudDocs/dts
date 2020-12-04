# ModifyConsumptionTimestamp

调用ModifyConsumptionTimestamp接口修改数据订阅实例的消费时间点。

**说明：**

-   本接口仅适用于旧版订阅通道。在调用[ConfigureSubscriptionInstance](~49437~)接口配置数据订阅通道时，如果不传入了**SubscriptionInstanceNetworkType**参数，则订阅通道被定义为旧版；如果传入则订阅通道被定义为新版。
-   如果订阅通道为新版，您需要在客户端自定义消费时间点。
-   调用本接口时，需要停止消费的客户端，并且数据订阅通道需处于正常状态。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=Dts&api=ModifyConsumptionTimestamp&type=RPC&version=2020-01-01)

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|Action|String|是|ModifyConsumptionTimestamp|系统规定参数，取值：**ModifyConsumptionTimestamp**。 |
|ConsumptionTimestamp|String|是|2019-10-15T17:20:03Z|消费时间点，格式为*yyyy-MM-dd*T*HH:mm:ss*Z（UTC时间）。 |
|SubscriptionInstanceId|String|是|dtsg2m10r1x15a\*\*\*\*|数据订阅实例ID，可以通过调用**DescribeSubscriptionInstances**接口查询。 |
|RegionId|String|否|cn-hangzhou|地域ID，传入本参数来指定实例所在地域，详情请参见[支持的地域列表](~141033~)。 |
|AccountId|String|否|12323344\*\*\*\*|阿里云主账号ID，无需设置，该参数即将下线。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|ErrCode|String|InternalError|调用出错时返回的错误码。 |
|ErrMessage|String|The request processing has failed due to some unknown error.|调用出错时返回的错误信息。 |
|RequestId|String|ABBACEFC-CBA9-4F80-A337-42F202F5\*\*\*\*|请求ID。 |
|Success|String|true|请求是否成功。 |

## 示例

请求示例

```
http(s)://dts.aliyuncs.com/?Action=ModifyConsumptionTimestamp
&ConsumptionTimestamp=2019-10-15T17:20:03Z
&SubscriptionInstanceId=dtsg2m10r1x15a****
&<公共请求参数>
```

正常返回示例

`XML` 格式

```
<ModifyConsumptionTimestampResponse>
      <RequestId>ABBACEFC-CBA9-4F80-A337-42F202F5****</RequestId>
      <Success>true</Success>
</ModifyConsumptionTimestampResponse>
```

`JSON` 格式

```
{
	"RequestId": "ABBACEFC-CBA9-4F80-A337-42F202F5****",
	"Success": true
}
```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/Dts)查看更多错误码。

