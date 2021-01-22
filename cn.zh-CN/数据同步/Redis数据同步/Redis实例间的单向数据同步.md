# Redis实例间的单向数据同步

数据传输服务DTS（Data Transmission Service）支持Redis数据库的单向同步，适用于异地多活、数据异地容灾等多种应用场景。本文以Redis实例间的单向数据同步为例，介绍数据同步作业的配置流程。

**警告：** 完成数据同步作业的配置后，请勿变更源数据库或目标数据库的[架构类型](https://help.aliyun.com/document_detail/86132.html)（例如将主从架构变更为集群架构），否则会导致数据同步失败。

## 前提条件

目标Redis实例的存储空间需大于源Redis实例已使用的存储空间。

## 注意事项

-   DTS在执行全量数据初始化时将占用源库和目标库一定的资源，可能会导致数据库服务器负载上升。如果数据库业务量较大或服务器规格较低，可能会加重数据库压力，甚至导致数据库服务不可用。建议您在执行数据同步前谨慎评估，在业务低峰期执行数据同步。
-   当源Redis实例的版本为2.8时不支持增量同步。
-   数据同步期间，请勿在源库中执行`FLUSHDB`和`FLUSHALL`命令，否则将导致源和目标的数据不一致。
-   如果目标库的数据逐出策略（`maxmemory-policy`）配置为`noeviction`以外的值，可能导致目标库的数据与源库不一致。关于数据逐出策略详情，请参见[Redis数据逐出策略介绍](/cn.zh-CN/用户指南/常见问题/ApsaraDB for Redis默认的数据逐出策略是什么？.md)。

## 支持的同步拓扑

-   一对一单向同步
-   一对多单向同步
-   级联单向同步

关于各类同步拓扑的介绍及注意事项，请参见[数据同步拓扑介绍](/cn.zh-CN/数据同步/数据同步拓扑介绍.md)。

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
-   SWAPDB、UNLINK（仅当源端Redis实例的版本为4.0时支持）

**说明：**

-   对于通过EVAL或者EVALSHA调用Lua脚本，在增量数据同步时，由于目标端在执行脚本时不会明确返回执行结果，DTS无法确保该类型脚本能够执行成功。
-   对于List，由于DTS在调用sync或psync进行重传时，不会对目标端已有的数据进行清空，可能导致出现重复数据。

## 数据库账号的权限要求

|数据库|权限及授权方式|
|---|-------|
|源Redis实例|读权限，关于授权方式，请参见[创建与管理账号](/cn.zh-CN/用户指南/账号与安全/创建与管理账号.md)。|
|目标Redis实例|读写权限，关于授权方式，请参见[创建与管理账号](/cn.zh-CN/用户指南/账号与安全/创建与管理账号.md)。|

## 操作步骤

1.  购买数据同步实例，详情请参见[购买数据同步作业](/cn.zh-CN/快速入门/购买流程.md)。

    **说明：** 购买时，源实例和目标实例均选择为**Redis**。

2.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
3.  在左侧导航栏，单击**数据同步**。
4.  在同步作业列表页面顶部，选择同步的目标实例所属地域。

    ![选择地域](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p50604.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。
6.  配置数据同步的源实例及目标实例信息。

    ![源库和目标库信息配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2249459951/p50605.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |无|同步作业名称|DTS会自动生成一个同步作业名称，建议配置具有业务意义的名称（无唯一性要求），便于后续识别。|
    |源实例信息|实例类型|选择**Redis实例**。|
    |实例地区|购买数据同步实例时选择的源实例地域信息，不可变更。|
    |实例ID|选择源Redis实例ID。|
    |数据库密码|填入连接源Redis实例的数据库账号密码。账号权限，请参见[数据库账号的权限要求](#section_3mt_iy9_yua)。**说明：** 数据库密码格式为<user\>:<password\>。例如，Redis实例自定义的用户名为admin，密码为Rp829dlwa，则此处填入的数据库密码为admin:Rp829dlwa。 |
    |目标实例信息|实例类型|选择**Redis实例**。|
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |实例ID|选择目标Redis实例ID。|
    |数据库密码|填入连接目标Redis实例的数据库账号密码。 账号权限，请参见[数据库账号的权限要求](#section_3mt_iy9_yua)。**说明：** 数据库密码格式为<user\>:<password\>。例如，Redis实例自定义的用户名为admin，密码为Rp829dlwa，则此处填入的数据库密码为admin:Rp829dlwa。 |

7.  单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到源和目标Redis实例的白名单中，用于保障DTS服务器能够正常连接源和目标Redis实例。

8.  配置目标已存在表的处理模式和同步对象。

    ![配置处理模式和同步对象](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1349459951/p50649.png)

    |配置|说明|
    |:-|:-|
    |目标已存在表的处理模式|    -   **预检查并报错拦截**：检查目标库是否为空。如果待同步的目标库为空，则通过该检查项目；如果不为空，则在预检查阶段提示错误，数据同步作业不会被启动。
    -   **忽略报错并继续执行**：跳过目标库是否为空的检查项。

**警告：** 选择为**忽略报错并继续执行**后，如果在同步过程中遇到目标库中的Key与源库中的Key相同，会将源库的数据覆盖写入目标库中，请谨慎选择。 |
    |同步对象|    -   在源库对象框中单击待同步的数据库，然后单击![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p40698.png)将其移动到已选择对象框。
    -   同步对象的选择粒度为库，暂不支持Key粒度的选择。 |

9.  上述配置完成后单击页面右下角的**下一步**。
10. 配置同步初始化选项，当前固定为**包含全量数据+增量数据**。

    ![Redis同步初始化](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p50606.png)

    **说明：**

    -   DTS会将源Redis实例中的存量数据同步至目标Redis实例中，并同步增量数据。
    -   如果跳出版本相关的报错提示，请您按照提示将源Redis实例升级至指定版本。关于升级版本方式，请参见[升级大版本](/cn.zh-CN/用户指南/管理实例/管理生命周期/升级大版本.md)和[升级小版本](/cn.zh-CN/用户指南/管理实例/管理生命周期/升级小版本.md)。
11. 上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：**

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![提示](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p47468.png)，查看失败详情。根据提示修复后，重新进行预检查。
12. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，同步作业将正式开始。
13. 等待同步作业的链路初始化完成，直至处于**同步中**状态。

    ![数据同步状态](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1349459951/p41059.png)

    **说明：** 您可以在数据同步页面，查看数据同步作业的状态。


