# DescribeSubscriptionInstances

查看阿里云账号有权限的订阅通道列表，返回所有状态的数据订阅实例。

## 请求参数

|参数说明|数据类型|是否必须|参数说明|
|----|----|----|----|
|Action|String|是|要执行的操作，取值：**DescribeSubscriptionInstances**。|
|PageSize|Integer|否|每页记录数，取值：**30**、**50**或**100**，默认值：**30**。|
|PageNum|Integer|否|页码。

大于0，且不超过Integer的最大值，默认值为**1**。 |

## 返回参数

|参数名称|数据类型|参数说明|
|----|----|----|
|SubscriptionInstances|List|订阅通道详情，详情请参见[查看订阅通道状态](/cn.zh-CN/API参考/数据订阅/查看订阅通道状态.md)。|
|PageNumber|Integer|页码。|
|TotalRecrodCount|Integer|总记录数。|
|PageRecordCount|Integer|本页记录个数。|

## 示例

```
    public List<SubscriptionInstance> list(String jobName) throws ClientException {
        DescribeSubscriptionInstancesRequest request = new DescribeSubscriptionInstancesRequest();
        request.setPageNum(1);
        request.setPageSize(20);
        if(jobName!=null) {
            request.setSubscriptionInstanceName(jobName);
        }
        DescribeSubscriptionInstancesResponse response = client.getAcsResponse(request);
        System.out.println("page no: "+response.getPageNumber());
        System.out.println("page size: "+response.getPageRecordCount());
        System.out.println("size: "+ response.getSubscriptionInstances().size());
        return response.getSubscriptionInstances();
    }
```

