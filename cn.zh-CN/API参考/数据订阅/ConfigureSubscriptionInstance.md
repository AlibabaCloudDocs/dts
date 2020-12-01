# ConfigureSubscriptionInstance

调用ConfigureSubscriptionInstance接口配置订阅通道。

调用本接口时，订阅通道需处于**未配置**状态。

**说明：** 如您暂未拥有订阅通道，您可以通过调用[CreateSubscriptionInstance](https://help.aliyun.com/document_detail/49436.html)接口创建。

## 注意事项

-   在成功调用[StartSubscriptionInstance](/cn.zh-CN/API参考/数据订阅/启动订阅通道.md)接口启动订阅通道之前，您可以重复调用本接口修改订阅通道的配置。
-   如果传入了SubscriptionInstanceNetworkType参数则订阅通道被定义为新版，如果不传入该参数则订阅通道被定义为旧版。

    **说明：**

    -   旧版订阅通道支持订阅RDS MySQL、DRDS的数据。
    -   新版订阅通道支持订阅RDS MySQL、PolarDB MySQL和Oracle的数据。

## 请求参数

|名称|类型|是否必选|示例值|描述|
|:-|:-|:---|:--|:-|
|Action|String|是|ConfigureSubscriptionInstance|要执行的操作，取值：ConfigureSubscriptionInstance。|
|SubscriptionInstanceId|String|是|dtsxxxxxxxx|订阅实例ID。|
|SourceEndpointInstanceId|String|是|rm-mxxxxxxxx|待订阅的实例ID。|
|SubscriptionObjects|List|是|\[\{"DBName":"dtstestdata","TableIncludes":\[\{"TableName":"customer"\}\]\}\]|订阅对象，格式为JSON串，详情请参见[订阅对象配置说明](/cn.zh-CN/API参考/参数补充说明/订阅对象配置说明.md)。|
|SubscriptionInstanceName|String|否|测试MySQL|订阅通道名称。|
|SourceEndpointInstanceType|String|否|RDS|数据源的实例类型，取值： -   **RDS**：RDS实例
-   **PolarDB**：PolarDB MySQL集群

**说明：** 默认取值为**RDS**。 |
|SubscriptionInstanceNetworkType|String|否|VPC|订阅通道的网络类型，取值： -   **classic**：经典网络
-   **VPC**：专有网络

**说明：**

-   如果传入了本参数则订阅通道被定义为新版，如果不传入该参数则订阅通道被定义为旧版。
-   旧版订阅通道支持订阅RDS for MySQL、DRDS和Oracle的数据，新版订阅通道支持订阅RDS for MySQL和Oracle的数据。
-   本参数传入的值为**VPC**时，您还需要传入**SubscriptionInstance.VPCId**和**SubscriptionInstance. VSwitchID**参数。 |
|SubscriptionInstance.VPCId|String|否|vpc-bpxxxxxxxx|订阅通道的专有网络（VPC）ID。 **说明：** 当**SubscriptionInstanceNetworkType**参数值为**VPC**时，本参数才可用。 |
|SubscriptionInstance. VSwitchID|String|否|vsw-bpxxxxxxxx|订阅通道的虚拟交换机ID。 **说明：** 当**SubscriptionInstanceNetworkType**参数值为**VPC**时，本参数才可用。 |
|SubscriptionDataType.DML|Boolean|否|True|是否订阅DML类型的数据，取值： -   **True**：订阅DML类型的数据
-   **False**：不订阅DML类型的数据

**说明：** 默认取值为**True**。 |

## 返回参数

|名称|类型|示例值|描述|
|:-|:-|:--|:-|
|RequestId|String|36923CC2-DDAB-4B48-A144-DA92C1E19537|请求ID。|

## 示例

```
    public void configureSubscriptionJob(String subscriptionJobId, String jobName, String region, String rdsInstanceId, String rdsUsername, String rdsPassword, String netType) throws Exception {
        ConfigureSubscriptionInstanceRequest request = new ConfigureSubscriptionInstanceRequest();

        // The subscription jobid created before
        request.setSubscriptionInstanceId(subscriptionJobId);

        // The subscription job name
        request.setSubscriptionInstanceName(jobName);

        request.setSourceEndpointInstanceType("RDS");
        request.setSourceEndpointUserName(rdsUsername);
        request.setSourceEndpointPassword(rdsPassword);
        request.setSourceEndpointInstanceID(rdsInstanceId);

        // Subscription DDL update
        request.setSubscriptionDataTypeDDL(Boolean.TRUE);
        // Subscription DML update
        request.setSubscriptionDataTypeDML(Boolean.TRUE);

        request.setSubscriptionObject("[{\"DBName\":\"portal_test\",\"NewDBName\":\"portal_test\",\"TableIncludes\":[{\"TableName\":\"test\",\"NewTableName\":\"test\"}]}]");

        request.setSubscriptionInstanceNetworkType(netType);

        ConfigureSubscriptionInstanceResponse response = client.getAcsResponse(request);
        if (!"true".equals(response.getSuccess())) {
            throw new Exception(response.getErrMessage() + " requestId=" + response.getErrCode());
        }
    }
            
```

## 错误码

|错误码|错误信息|描述|
|:--|:---|:-|
|InvalidParameter|The Input Parameter$\{parameter\} is not valid.|输入的参数不合法，请检查输入的参数是否正确。|
|InvalidParameterValid|The Value of Input Parameter$\{parameter\} is not valid.|参数值校验不通过，请检查是否缺失所提示的参数以及用户AK是否正确。|
|MissingParameter|The parameter "<parameter name\>" is needed but no provided.|缺少参数，请检查是否缺失所提示的参数以及用户AK是否正确，具体请参照DTS OpenAPI文档。|
|Forbidden.InstanceNotFound|The specified Instance Not Found.|RDS实例不存在或当前用户没有该实例的操作权限，请检查实例ID或权限是否正确。|
|OperationDenied.DBInstanceType|The operation is not permitted due to the instance is Read only.|RDS实例是只读实例，不允许执行当前操作。|
|OperationDenied.DBInstanceStatus|The operation is not permitted due to status of instance.|实例状态不允许执行当前操作，请检查当前的实例状态是否正确。|

