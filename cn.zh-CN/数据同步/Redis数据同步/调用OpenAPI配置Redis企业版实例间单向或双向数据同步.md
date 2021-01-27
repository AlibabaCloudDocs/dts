# 调用OpenAPI配置Redis企业版实例间单向或双向数据同步

您可以通过调用DTS的相关接口，为Redis企业版实例配置单向或双向数据同步。本文介绍相关注意事项及示例代码。

本文展示的示例代码基于Java语言，您需要将示例代码中的`<accessKeyId>`和`<accessSecret>`分别替换为您的AccessKeyId和AccessKeySecret。您也可以在[OpenAPI Explorer](https://api.aliyun.com/#/?product=Dts)中直接运行相关接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。

## 前提条件

源和目标实例为[阿里云Redis企业版](https://help.aliyun.com/document_detail/145957.html)实例（5.0版本）。

**说明：** 架构类型不限制，即支持**集群版**、**标准版**和**读写分离版**。

## 注意事项

-   双向数据同步时，正向数据同步作业会执行 和 ，反向数据同步作业仅执行增量数据同步。

    **警告：** 为保障数据一致性，双向数据同步作业运行期间，请勿在两端数据库同时对同一个key执行修改或写入操作。

-   DTS在执行全量数据初始化时将占用源库和目标库一定的资源，可能会导致数据库服务器负载上升。如果数据库业务量较大或服务器规格较低，可能会加重数据库压力，甚至导致数据库服务不可用。建议您在执行数据迁移前谨慎评估，在业务低峰期执行数据同步。
-   请勿在源实例中执行`FLUSHDB`和`FLUSHALL`命令，否则将导致源和目标的数据不一致。
-   如果目标库的数据逐出策略（`maxmemory-policy`）配置为`noeviction`以外的值，可能导致目标库的数据与源库不一致。关于数据逐出策略详情，请参见[Redis数据逐出策略介绍](/cn.zh-CN/用户指南/常见问题/ApsaraDB for Redis默认的数据逐出策略是什么？.md)。
-   如果源库中的某些Key使用了过期（expire）策略，由于可能存在Key已过期但未被及时删除的情况，所以在目标库中查看到的Key数量（例如通过info命令查看）会比源库的Key数量少。

    **说明：** 源和目标库中，未设置过期策略或未过期的Key数量是一致的。

-   如果目标Redis实例没有开通直连访问，DTS将采用代理转发模式将数据写入目标实例。

    **说明：** 关于直连访问的开通方法，请参见[开通直连访问](/cn.zh-CN/用户指南/管理实例/管理网络连接/开通直连访问.md)。


## 功能限制

不支持级联单向同步，相关介绍请参见[数据同步拓扑介绍](/cn.zh-CN/数据同步/数据同步拓扑介绍.md)。

## 支持的同步命令

-   APPEND
-   BITOP、BLPOP、BRPOP、BRPOPLPUSH
-   DECR、DECRBY、DEL
-   EVAL、EVALSHA、EXEC、EXPIRE、EXPIREAT
-   GEOADD、GETSET
-   HDEL、HINCRBY、HINCRBYFLOAT、HMSET、HSET、HSETNX
-   INCR、INCRBY、INCRBYFLOAT
-   LINSERT、LPOP、LPUSH、LPUSHX、LREM、LSET、LTRIM
-   MOVE、MSET、MSETNX、MULTI
-   PERSIST、PEXPIRE、PEXPIREAT、PFADD、PFMERGE、PSETEX、PUBLISH
-   RENAME、RENAMENX、RPOP、RPOPLPUSH、RPUSH、RPUSHX
-   SADD、SDIFFSTORE、SELECT、SET、SETBIT、SETEX、SETNX、SETRANGE、SINTERSTORE、SMOVE、SPOP、SREM、SUNIONSTORE
-   UNLINK、ZADD、ZINCRBY、ZINTERSTORE、ZREM、ZREMRANGEBYLEX、ZUNIONSTORE、ZREMRANGEBYRANK、ZREMRANGEBYSCORE
-   SWAPDB（当源或目标实例的架构为集群版时不支持）

**说明：**

-   对于通过EVAL或者EVALSHA调用Lua脚本，在增量数据同步时，由于目标端在执行脚本时不会明确返回执行结果，DTS无法确保该类型脚本能够执行成功。
-   对于List，由于DTS在调用sync或psync进行重传时，不会对目标端已有的数据进行清空，可能导致出现重复数据。

## 准备工作

1.  [创建AccessKey](https://help.aliyun.com/document_detail/53045.html)。

    **说明：** 为避免主账号泄露AccessKey带来的安全风险，建议您创建RAM用户，授予RAM用户访问DTS资源的权限，再使用RAM用户的AccessKey来调用SDK。详情请参见[账号访问控制](https://help.aliyun.com/document_detail/25481.html)。

2.  [下载SDK](/cn.zh-CN/SDK参考/SDK下载.md)。相关安装方法及介绍，请参见[DTS Java SDK使用说明](/cn.zh-CN/SDK参考/DTS Java SDK使用说明.md)。

## 创建数据同步作业

本示例中，请求参数和返回参数的详细说明请参见[t17260.md\#](/cn.zh-CN/API参考/数据同步/购买同步实例.md)。

**说明：** `Topology`参数传入oneway，则创建单向数据同步作业；传入bidirectional则创建双向数据同步作业。

请求示例：

```
import com.aliyuncs.DefaultAcsClient;
import com.aliyuncs.IAcsClient;
import com.aliyuncs.exceptions.ClientException;
import com.aliyuncs.exceptions.ServerException;
import com.aliyuncs.profile.DefaultProfile;
import com.google.gson.Gson;
import java.util.*;
import com.aliyuncs.dts.model.v20180801.*;

public class CreateSynchronizationJob {

    public static void main(String[] args) {
        DefaultProfile profile = DefaultProfile.getProfile("cn-hangzhou", "<accessKeyId>", "<accessSecret>");
        IAcsClient client = new DefaultAcsClient(profile);

        CreateSynchronizationJobRequest request = new CreateSynchronizationJobRequest();
        request.setRegionId("cn-hangzhou");
        request.setSourceRegion("cn-beijing");
        request.setDestRegion("cn-beijing");
        request.setSynchronizationJobClass("small");
        request.setPayType("Postpaid");
        request.setTopology("oneway");
        request.setSourceEndpointInstanceType("Redis");
        request.setDestinationEndpointInstanceType("Redis");

        try {
            CreateSynchronizationJobResponse response = client.getAcsResponse(request);
            System.out.println(new Gson().toJson(response));
        } catch (ServerException e) {
            e.printStackTrace();
        } catch (ClientException e) {
            System.out.println("ErrCode:" + e.getErrCode());
            System.out.println("ErrMsg:" + e.getErrMsg());
            System.out.println("RequestId:" + e.getRequestId());
        }

    }
}
```

返回示例：

```
{
    "SynchronizationJobId": "dts********",
    "RequestId": "158347*****-rqyjQ",
    "Success": true
}
```

## 配置数据同步作业（单向）

本示例中，源实例的数据库账号需具备读权限，目标实例的数据库账号需具备读写权限。

**说明：** 配置单向数据同步时，您也可以将自建Redis数据库或阿里云Redis社区版实例作为源库或目标库，配置方法与本案例类似（需调整对应的请求参数，例如`MigrationReserved`）。

关于请求参数和返回参数的详细说明请参见[t17259.md\#](/cn.zh-CN/API参考/数据同步/配置同步作业.md)。

请求示例：

```
import com.aliyuncs.DefaultAcsClient;
import com.aliyuncs.IAcsClient;
import com.aliyuncs.exceptions.ClientException;
import com.aliyuncs.exceptions.ServerException;
import com.aliyuncs.profile.DefaultProfile;
import com.google.gson.Gson;
import java.util.*;
import com.aliyuncs.dts.model.v20180801.*;

public class ConfigureSynchronizationJob {

    public static void main(String[] args) {
        DefaultProfile profile = DefaultProfile.getProfile("cn-hangzhou", "<accessKeyId>", "<accessSecret>");
        IAcsClient client = new DefaultAcsClient(profile);

        ConfigureSynchronizationJobRequest request = new ConfigureSynchronizationJobRequest();
        request.setRegionId("cn-hangzhou");
        request.setSynchronizationJobId("dts*******");
        request.setStructureInitialization(true);
        request.setDataInitialization(true);
        request.setSynchronizationObjects("[{\"DBName\": \"0\",\"NewDBName\": \"0\"}]");
        request.setSynchronizationJobName("apitest");
        request.setSourceEndpointInstanceId("r-2ze********");
        request.setSourceEndpointInstanceType("redis");
        request.setSourceEndpointPassword("Test******");
        request.setDestinationEndpointInstanceId("r-2ze********");
        request.setDestinationEndpointInstanceType("redis");
        request.setDestinationEndpointPassword("Test******");
        request.setMigrationReserved("{\"srcRedisType\":\"enterprise\",\"destRedisType\":\"enterprise\"}");

        try {
            ConfigureSynchronizationJobResponse response = client.getAcsResponse(request);
            System.out.println(new Gson().toJson(response));
        } catch (ServerException e) {
            e.printStackTrace();
        } catch (ClientException e) {
            System.out.println("ErrCode:" + e.getErrCode());
            System.out.println("ErrMsg:" + e.getErrMsg());
            System.out.println("RequestId:" + e.getRequestId());
        }

    }
}
```

返回示例：

```
{
    "RequestId": "1583********-BcO5F",
    "Success": true
}
```

## 配置数据同步作业（双向）

本示例中，用于数据同步的数据库账号需具备读写权限。

关于请求参数和返回参数的详细说明请参见[t17259.md\#](/cn.zh-CN/API参考/数据同步/配置同步作业.md)。

请求示例：

1.  配置正向数据同步作业。

    ```
    import com.aliyuncs.DefaultAcsClient;
    import com.aliyuncs.IAcsClient;
    import com.aliyuncs.exceptions.ClientException;
    import com.aliyuncs.exceptions.ServerException;
    import com.aliyuncs.profile.DefaultProfile;
    import com.google.gson.Gson;
    import java.util.*;
    import com.aliyuncs.dts.model.v20180801.*;
    
    public class ConfigureSynchronizationJob {
    
        public static void main(String[] args) {
            DefaultProfile profile = DefaultProfile.getProfile("cn-hangzhou", "<accessKeyId>", "<accessSecret>");
            IAcsClient client = new DefaultAcsClient(profile);
    
            ConfigureSynchronizationJobRequest request = new ConfigureSynchronizationJobRequest();
            request.setRegionId("cn-hangzhou");
            request.setSynchronizationJobId("dts*******");
            request.setStructureInitialization(true);
            request.setDataInitialization(true);
            request.setSynchronizationObjects("[{\"DBName\": \"0\",\"NewDBName\": \"0\"}]");
            request.setSynchronizationJobName("apitest");
            request.setSynchronizationDirection("Forward");
            request.setSourceEndpointInstanceId("r-2ze********");
            request.setSourceEndpointInstanceType("redis");
            request.setSourceEndpointPassword("Test******");
            request.setDestinationEndpointInstanceId("r-2ze********");
            request.setDestinationEndpointInstanceType("redis");
            request.setDestinationEndpointPassword("Test******");
            request.setMigrationReserved("{\"srcRedisType\":\"enterprise\",\"destRedisType\":\"enterprise\"}");
    
            try {
                ConfigureSynchronizationJobResponse response = client.getAcsResponse(request);
                System.out.println(new Gson().toJson(response));
            } catch (ServerException e) {
                e.printStackTrace();
            } catch (ClientException e) {
                System.out.println("ErrCode:" + e.getErrCode());
                System.out.println("ErrMsg:" + e.getErrMsg());
                System.out.println("RequestId:" + e.getRequestId());
            }
    
        }
    }
    ```

2.  等待正向数据同步作业初始化完成（即转变为**同步中**状态）。

    **说明：** 您可以通过控制台或调用[t17263.md\#](/cn.zh-CN/API参考/数据同步/查看同步作业状态.md)接口查看数据同步作业的状态信息。

3.  配置反向数据同步作业。

    ```
    import com.aliyuncs.DefaultAcsClient;
    import com.aliyuncs.IAcsClient;
    import com.aliyuncs.exceptions.ClientException;
    import com.aliyuncs.exceptions.ServerException;
    import com.aliyuncs.profile.DefaultProfile;
    import com.google.gson.Gson;
    import java.util.*;
    import com.aliyuncs.dts.model.v20180801.*;
    
    public class ConfigureSynchronizationJob {
    
        public static void main(String[] args) {
            DefaultProfile profile = DefaultProfile.getProfile("cn-hangzhou", "<accessKeyId>", "<accessSecret>");
            IAcsClient client = new DefaultAcsClient(profile);
    
            ConfigureSynchronizationJobRequest request = new ConfigureSynchronizationJobRequest();
            request.setRegionId("cn-hangzhou");
            request.setSynchronizationJobId("dts*******");
            request.setStructureInitialization(true);
            request.setDataInitialization(true);
            request.setSynchronizationObjects("[{\"DBName\": \"0\",\"NewDBName\": \"0\"}]");
            request.setSynchronizationJobName("apitest");
            request.setSynchronizationDirection("Reverse");
            request.setSourceEndpointInstanceId("r-2ze********");
            request.setSourceEndpointInstanceType("redis");
            request.setSourceEndpointPassword("Test******");
            request.setDestinationEndpointInstanceId("r-2ze********");
            request.setDestinationEndpointInstanceType("redis");
            request.setDestinationEndpointPassword("Test******");
            request.setMigrationReserved("{\"srcRedisType\":\"enterprise\",\"destRedisType\":\"enterprise\"}");
    
            try {
                ConfigureSynchronizationJobResponse response = client.getAcsResponse(request);
                System.out.println(new Gson().toJson(response));
            } catch (ServerException e) {
                e.printStackTrace();
            } catch (ClientException e) {
                System.out.println("ErrCode:" + e.getErrCode());
                System.out.println("ErrMsg:" + e.getErrMsg());
                System.out.println("RequestId:" + e.getRequestId());
            }
    
        }
    }
    ```


返回示例：

```
{
    "RequestId": "1583********-BcO5F",
    "Success": true
}
```

## 相关文档

-   [查看同步链路状态和性能](/cn.zh-CN/数据同步/同步作业管理/查看同步链路状态和性能.md)

    通过全量同步性能和增量同步性能展示功能，您可以了解数据同步作业的运行状态，为您管理数据同步作业提供参考依据。

-   [新增同步对象](/cn.zh-CN/数据同步/同步作业管理/新增同步对象.md)/[移除同步对象](/cn.zh-CN/数据同步/同步作业管理/移除同步对象.md)

    在数据同步过程中，您可以新增或移除同步对象。

-   [配置监控报警](/cn.zh-CN/数据同步/同步作业管理/配置监控报警.md)

    您可以对重要的监控指标设置报警规则，让您第一时间了解同步作业的状态。


