# 从ECS上的Twemproxy Redis集群同步至Redis实例

阿里云数据库Redis版是兼容开源Redis协议标准、提供内存加硬盘混合存储的数据库服务，基于高可靠双机热备架构及可平滑扩展的集群架构，可充分满足高吞吐、低延迟及弹性变配的业务需求。通过数据传输服务DTS（Data Transmission Service），您可以将Twemproxy Redis同步至阿里云Redis实例。

-   已创建目标阿里云Redis实例，详情请参见[创建Redis实例](/intl.zh-CN/快速入门/步骤1：创建实例.md)。
-   阿里云Redis实例的存储空间需大于源Twemproxy Redis数据库已使用的存储空间。
-   源Twemproxy Redis集群中的每个Master节点必须能够执行`psync`命令。

## 同步原理介绍

DTS通过同步Twemproxy Redis集群中的每个Redis-Server来实现集群的整体同步，您需要为每个Redis-Server创建一个对应的数据同步作业。

![twemproxy同步原理](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p71490.png)

## Twemproxy Redis集群环境介绍

在本案例中，Twemproxy Redis集群具备两个Redis-Server，每个Redis-Server采用一主一从的架构，详情请参见下图。

![twemproxy环境介绍](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p71496.png)

## 注意事项

-   DTS在执行全量数据初始化时将占用源库和目标库一定的资源，可能会导致数据库服务器负载上升。如果数据库业务量较大或服务器规格较低，可能会加重数据库压力，甚至导致数据库服务不可用。建议您在执行数据同步前谨慎评估，在业务低峰期执行数据同步。
-   如果在源数据库的配置文件redis.conf中配置了`bind`参数，请将该参数的值设置为ECS的内网IP地址以保障DTS可以正常连接源数据库。
-   为保障同步链路稳定性，建议将源集群的配置文件redis.conf中`repl-backlog-size`参数的值适当调大。
-   为保障同步质量，DTS会在源集群中插入一个key：`DTS_REDIS_TIMESTAMP_HEARTBEAT`，用于记录更新时间点。
-   请勿在源集群中执行`FLUSHDB`和`FLUSHALL`命令，否则将导致源和目标的数据不一致。
-   如果目标库的数据逐出策略（`maxmemory-policy`）配置为`noeviction`以外的值，可能导致目标库的数据与源库不一致。关于数据逐出策略详情，请参见[Redis数据逐出策略介绍](/intl.zh-CN/用户指南/常见问题/ApsaraDB for Redis默认的数据逐出策略是什么？.md)。
-   如果源库中的某些Key使用了过期（expire）策略，由于可能存在Key已过期但未被及时删除的情况，所以在目标库中查看到的Key数量（例如通过info命令查看）会比源库的Key数量少。

    **说明：** 源和目标库中，未设置过期策略或未过期的Key数量是一致的。

-   目标阿里云Redis实例支持的版本为2.8、4.0或5.0版本，如需跨版本同步（仅支持从低版本同步到高版本）请提前确认兼容性。例如创建按量付费的Redis实例来测试，测试完成后可将该实例释放或转为包年包月。

## 支持的同步拓扑

-   一对一单向同步
-   一对多单向同步
-   级联单向同步

关于各类同步拓扑的介绍及注意事项，请参见[数据同步拓扑介绍](/intl.zh-CN/数据同步/数据同步拓扑介绍.md)。

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
-   RENAME、RENAMENX、RESTORE、RPOP、RPOPLPUSH、RPUSH、RPUSHX
-   SADD、SDIFFSTORE、SELECT、SET、SETBIT、SETEX、SETNX、SETRANGE、SINTERSTORE、SMOVE、SPOP、SREM、SUNIONSTORE
-   ZADD、ZINCRBY、ZINTERSTORE、ZREM、ZREMRANGEBYLEX、ZUNIONSTORE、ZREMRANGEBYRANK、ZREMRANGEBYSCORE

**说明：**

-   对于通过EVAL或者EVALSHA调用Lua脚本，在增量数据同步时，由于目标端在执行脚本时不会明确返回执行结果，DTS无法确保该类型脚本能够执行成功。
-   对于List，由于DTS在调用sync或psync进行重传时，不会对目标端已有的数据进行清空，可能导致出现重复数据。

## 操作步骤

1.  购买数据同步作业，详情请参见[购买流程]()。

    **说明：** 购买时选择源实例为**Redis**、目标实例为**Redis**，并选择同步拓扑为**单向同步**。

2.  登录[数据传输控制台](https://dts-intl.console.aliyun.com/)。

3.  在左侧导航栏，单击**数据同步**。

4.  在同步作业列表页面顶部，选择同步的目标实例所属地域。

    ![选择地域](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p50604.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。

6.  配置数据同步的源实例及目标实例信息。

    ![配置同步的源和目标实例信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p71477.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |无|同步作业名称|DTS会自动生成一个同步作业名称，建议配置具有业务意义的名称（无唯一性要求），便于后续识别。|
    |源实例信息|实例类型|选择**ECS上的自建数据库**。|
    |实例地区|购买数据同步实例时选择的源实例地域，不可变更。|
    |ECS实例ID|选择源集群Redis-Server中Master节点所属的ECS实例ID。 **说明：** DTS通过同步Twemproxy Redis集群中的每个Redis-Server来实现整体的数据同步，此处先填入第一个Redis-Server中Master节点所属的ECS实例ID；稍后配置第二个数据同步作业时，此处填入第二个Redis-Server中Master节点所属的ECS实例ID；以此类推，直至为所有Redis-Server配置同步作业。 |
    |数据库类型|固定为**Redis**。|
    |实例模式|选择为**单机版**。 **说明：** 由于Twemproxy Redis集群架构的特殊性，无法直接同步Twemproxy Redis集群，DTS需要通过同步Twemproxy Redis集群中的每个Redis-Server来实现整体的数据同步，所以此处需选择为**单机版**。 |
    |端口|填入该Redis-Server中Master节点的服务端口。|
    |数据库密码|填该Master节点的数据库密码。 **说明：** 非必填项，如果没有设置密码可以不填。 |
    |目标实例信息|实例类型|选择**Redis实例**。|
    |实例地区|购买数据同步实例时选择的目标实例地域，不可变更。|
    |实例ID|选择目标阿里云Redis实例ID。|
    |数据库密码|填入Redis实例的数据库密码。 **说明：** 数据库密码格式为<user\>:<password\>。例如，Redis实例自定义的用户名为admin，密码为Rp829dlwa，则此处填入的数据库密码为admin:Rp829dlwa。 |

7.  单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址，自动添加到源ECS实例的内网入方向规则和阿里云Redis实例的白名单中，用于保障DTS服务器能够正常连接源和目标实例。

8.  配置目标已存在表的处理模式和同步对象。

    ![配置同步信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p71478.png)

    |配置|说明|
    |:-|:-|
    |目标已存在表的处理模式|DTS通过同步Twemproxy Redis集群中的每个Redis-Server来实现整体的数据同步。为第1个Redis-Server配置数据同步时，如果Redis实例暂无数据，请选择**预检查并报错拦截**。为第2到N个Redis-Server配置数据同步时，必须选择为**忽略报错并继续执行**，否则将无法正常同步数据。 **说明：**

    -   **预检查并报错拦截**：检查目标库是否为空。如果待同步的目标库为空，则通过该检查项目；如果不为空，则在预检查阶段提示错误，数据同步作业不会被启动。
    -   **忽略报错并继续执行**：忽略预检查阶段中检测到目标库不为空的报错，继续执行数据同步。如果在同步过程中遇到目标库中的key与源库中的key相同，会将源库的数据覆盖写入目标库中。 |
    |同步对象|    -   在源库对象框中单击待同步的数据库，然后单击![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p40698.png)图标将其移动到已选择对象框。
    -   同步对象的选择粒度为库，暂不支持Key粒度的选择。 |

9.  上述配置完成后，单击页面右下角的**下一步**。

10. 配置同步初始化的选项。

    ![Redis同步初始化](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p50606.png)

    **说明：** 当前固定为**包含全量数据+增量数据**，即DTS会将源Twemproxy Redis集群中的存量数据同步至目标Redis数据库中，并同步增量数据。

11. 上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：**

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![提示](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p47468.png)图标，查看失败详情。根据提示修复后，重新进行预检查。
12. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，同步作业将正式开始。

13. 等待同步作业的链路初始化完成，直至处于**同步中**状态。

    ![同步中](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p71480.png)

    **说明：** 您可以在数据同步页面，查看数据同步作业的状态。

14. 重复第[1](#step_lf9_8o3_too)步到第13步的操作，为剩余的Redis-Server创建数据同步作业。


本案例的Twemproxy Redis集群具备两个Redis-Server，所以创建两个数据同步作业。如下图所示，这两个数据同步作业完成同步初始化后，已经都处于**同步中**状态。

![源集群同步中](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p71481.png)

本案例同步的数据库为DB0，通过[DMS登录Redis集群](~~43847~~)后与源Twemproxy Redis集群进行对比，Key总数源Twemproxy Redis集群一致。

![阿里云Redis实例的key数量](../images/p71484.png "阿里云Redis实例")

![源twemproxy redis的key数量](../images/p71483.png "源Twemproxy Redis集群")

