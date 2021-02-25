# RAM资源授权

默认情况下，阿里云账号对自己的资源拥有完整的操作权限，您可以通过访问控制RAM（Resource Access Management）将当前云账号下DTS资源的相关权限授予给RAM用户。

## 注意事项

为保障RAM鉴权的正常执行，建议将SDK升级至2.0.18及以上版本，详情请参见[Java SDK](https://github.com/aliyun/aliyun-openapi-java-sdk/tree/master/aliyun-java-sdk-dts)。

**说明：** 请尽量使用2.0.18及以上版本的SDK，除特殊情况，早期的版本将不再维护。

## 使用限制

当前仅支持资源（Resource）级别的授权，暂不支持操作（Action）级别的授权。

## DTS API的鉴权规则

使用RAM用户或者[临时安全令牌STS](~~28756~~)（Security Token Service）调用DTS的API接口时，RAM将根据该接口的语义和涉及到的资源来确认所需的权限，以确保调用者具备相应权限。

## 可授权的资源类型

|资源类型|授权策略中的资源描述方式|示例|
|:---|:-----------|--|
|Instance|acs:dts:$regionid:$accountid:instance/$instanceid

 acs:dts:$regionid:$accountid:instance/\*

|acs:dts:cn-hangzhou:1234567890123:instance/\*|

**说明：**

-   $regionid：地域ID，您也可以使用`*`进行模糊匹配。
-   $instanceid：指定的实例ID，您也可以使用`*`进行模糊匹配。
-   $account-id：云帐号ID，可以用`*`代替。

## 请求参数说明

2.0.18及以上版本SDK的每个API新增了`AccountId`参数，说明如下：

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|AccountId|String|否|1234567890123|使用临时安全令牌STS Token访问的资源所属的云账号ID。 **说明：** 如果权限策略中配置了云账号ID，本参数必须传入。 |

## 相关文档

-   [通过系统策略授权子账号管理DTS](/cn.zh-CN/访问控制/通过系统策略授权子账号管理DTS.md)
-   [通过自定义策略授权子账号管理DTS](/cn.zh-CN/访问控制/通过自定义策略授权子账号管理DTS.md)
-   [使用RAM角色的临时安全令牌（STS token）访问阿里云相关资源](~~93744~~)
-   [配置STS Token](~~59919~~)。

