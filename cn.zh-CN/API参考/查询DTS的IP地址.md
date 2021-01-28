# 查询DTS的IP地址

调用DescribeDTSIP查询DTS服务的IP地址。

如果**源或目标实例**是**本地自建数据库**，您需要调用该接口查询DTS服务的IP地址，然后将查询所得的IP地址添加至源或目标实例的安全设置（通常为您自建数据库的防火墙）中。具体添加方式及更多信息，请参见[迁移、同步或订阅本地数据库时需添加的IP白名单](https://help.aliyun.com/document_detail/84900.htm)。

**说明：** 如果**源或目标数据库**是**阿里云数据库实例**（例如RDS、MongoDB等）或**ECS上的自建数据库**，在配置源或目标实例信息时，如您点击**授权白名单并进入下一步**，DTS会自动添加对应的IP地址，您无需手动添加。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=Dts&api=DescribeDTSIP&type=RPC&version=2020-01-01)

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|Action|String|是|DescribeDTSIP|系统规定参数，取值：**DescribeDTSIP**。 |
|SourceEndpointRegion|String|是|cn-hangzhou|源实例所属地域ID，详情请参见[支持的地域列表](~~141033~~)。

 **说明：** 如果源实例为有公网IP的自建数据库，则可以传入**cn-hangzhou**或者离自建数据库物理距离最近的区域ID。 |
|RegionId|String|否|cn-hangzhou|DTS任务实例所在地域，详情请参见[支持的地域列表](~~141033~~)。 |
|DestinationEndpointRegion|String|否|cn-hangzhou|目标实例所属地域ID，详情请参见[支持的地域列表](~141033~)。

 **说明：** 如果目标实例为有公网IP的自建数据库，则可以传入**cn-hangzhou**或者离自建数据库物理距离最近的区域ID。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|DynamicCode|String|500|服务内部错误码，该参数即将下线。 |
|DynamicMessage|String|10.151.12.0/24,47.102.181.0/24,47.101.109.0/24,120.55.129.0/24,11.115.103.0/24,47.102.234.0/24|DTS服务的IP地址。 |
|ErrCode|String|InternalError|调用出错时返回的错误码。 |
|ErrMessage|String|The request processing has failed due to some unknown error.|调用错误时返回对应的错误信息。 |
|RequestId|String|0C99C0BE-F312-40FA-ADFA-4DC1166B\*\*\*\*|请求ID。 |
|Success|String|true|请求是否成功。 |

## 示例

请求示例

```
http(s)://dts.aliyuncs.com/?Action=DescribeDTSIP
&SourceEndpointRegion=cn-hangzhou
&<公共请求参数>
```

正常返回示例

`XML`格式

```
<DescribeDTSIPResponse>
      <RequestId>0C99C0BE-F312-40FA-ADFA-4DC1166B****</RequestId>
      <DynamicMessage>10.151.12.0/24,47.102.181.0/24,47.101.109.0/24,120.55.129.0/24,11.115.103.0/24,47.102.234.0/24</DynamicMessage>
      <ErrMessage></ErrMessage>
      <Success>true</Success>
      <ErrCode></ErrCode>
</DescribeDTSIPResponse>
```

`JSON`格式

```
{
	"RequestId": "0C99C0BE-F312-40FA-ADFA-4DC1166B****",
	"DynamicMessage": "10.151.12.0/24,47.102.181.0/24,47.101.109.0/24,120.55.129.0/24,11.115.103.0/24,47.102.234.0/24",
	"ErrMessage": "",
	"Success": true,
	"ErrCode": ""
}
```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/Dts)查看更多错误码。

