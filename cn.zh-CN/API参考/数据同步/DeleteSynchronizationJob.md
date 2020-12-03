# DeleteSynchronizationJob

调用DeleteSynchronizationJob释放数据同步实例。

**说明：** 数据同步实例一旦释放将无法找回。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=Dts&api=DeleteSynchronizationJob&type=RPC&version=2020-01-01)

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|Action|String|是|DeleteSynchronizationJob|系统规定参数，取值：**DeleteSynchronizationJob**。 |
|SynchronizationJobId|String|是|dtshn6107ve264\*\*\*\*|数据同步实例ID，可以通过调用**DescribeSynchronizationJobs**接口查询。 |
|RegionId|String|否|cn-hangzhou|地域ID，传入本参数来指定实例所在地域，详情请参见[支持的地域列表](~141033~)。 |
|AccountId|String|否|12323344\*\*\*\*|阿里云主账号ID，无需设置，该参数即将下线。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|ErrCode|String|InternalError|调用出错时返回的错误码。 |
|ErrMessage|String|The request processing has failed due to some unknown error.|调用错误时返回对应的错误信息。 |
|RequestId|String|C306C198-7807-409D-930A-D6CE6C32\*\*\*\*|请求ID。 |
|Success|String|true|请求是否成功。 |

## 示例

请求示例

```
http(s)://dts.aliyuncs.com/?Action=DeleteSynchronizationJob
&SynchronizationJobId=dtshn6107ve264****
&<公共请求参数>
```

正常返回示例

`XML` 格式

```
<DeleteSynchronizationJobResponse>
      <RequestId>C306C198-7807-409D-930A-D6CE6C32****</RequestId>
      <Success>true</Success>
</DeleteSynchronizationJobResponse>
```

`JSON` 格式

```
{
	"RequestId": "C306C198-7807-409D-930A-D6CE6C32****",
	"Success": true
}
```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/Dts)查看更多错误码。

