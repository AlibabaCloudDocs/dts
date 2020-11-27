# DescribeSubscriptionInstanceAlert

调用DescribeSubscriptionInstanceAlert接口查询订阅通道的监控报警设置详情。

## 请求参数

|名称|类型|是否必选|实例值|描述|
|:-|:-|:---|:--|:-|
|Action|String|是|DescribeSubscriptionInstanceAlert|要执行的操作，取值：DescribeSubscriptionInstanceAlert。|
|SubscriptionInstanceId|String|是|dtsxxxxxxxx|数据订阅实例ID。|

## 返回参数

|名称|类型|示例值|描述|
|:-|:-|:--|:-|
|SubscriptionInstanceName|String|MySQL|数据订阅通道名称。|
|DelayAlertStatus|String|enable|**订阅延迟**监控项的开关状态，返回值为： -   enable：启用。
-   disable：禁用。 |
|DelayAlertPhone|String|136xxxxxxxx,137xxxxxxxx|**订阅延迟**监控项的报警联系人手机号码。 **说明：** 如果有多个手机号码，将以英文逗号（,）分隔。 |
|DelayOverSeconds|String|20|**订阅延迟**监控项的报警阈值，单位为秒。|
|ErrorAlertStatus|String|enable|**订阅状态**监控项的开关状态，返回值为： -   enable：启用。
-   disable：禁用。 |
|ErrorAlertPhone|String|138xxxxxxxx,139xxxxxxxx|**订阅状态**监控项的报警联系人手机号码。 **说明：** 如果有多个手机号码，将以英文逗号（,）分隔。 |
|Success|Boolean|true|接口是否调用成功，返回值为： -   true：成功。
-   flase：失败。 |

## 示例

```
public DescribeSubscriptionInstanceAlertResponse describeSubscribeJobAlert(String MigrationJobId) throws Exception 
  {
    DescribeSubscriptionInstanceAlertRequest request = new DescribeSubscriptionInstanceAlertRequest();
        request.setSubscriptionInstanceId(MigrationJobId);

        DescribeSubscriptionInstanceAlertResponse response = client.getAcsResponse(request);

        if (!"true".equals(response.getSuccess()))
    {
      throw new Exception(response.getErrMessage() + " requestId=" + response.getRequestId());
    }
        return response;
    }
```

## 错误码

访问[错误码中心](https://error-center.aliyun.com/status/product/Dts)查看更多错误码信息。

