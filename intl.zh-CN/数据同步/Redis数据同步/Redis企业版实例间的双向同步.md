# Redis企业版实例间的双向同步

数据传输服务DTS（Data Transmission Service）支持Redis企业版实例间的双向同步，适用于异地多活、数据容灾等多种应用场景，本文介绍数据同步作业的配置流程。

## 前提条件

源和目标实例为[阿里云Redis企业版](https://www.alibabacloud.com/help/zh/doc-detail/145957.htm)实例（5.0版本）。

**说明：** 架构类型不限制，即支持**集群版**、**标准版**和**读写分离版**。

## 注意事项

-   双向数据同步时，正向数据同步作业会执行 和 ，反向数据同步作业仅执行增量数据同步。

    **警告：** 为保障数据一致性，双向数据同步作业运行期间，请勿在两端数据库同时对同一个key执行修改或写入操作。

-   DTS在执行全量数据初始化时将占用源库和目标库一定的资源，可能会导致数据库服务器负载上升。如果数据库业务量较大或服务器规格较低，可能会加重数据库压力，甚至导致数据库服务不可用。建议您在执行数据迁移前谨慎评估，在业务低峰期执行数据同步。
-   请勿在源实例中执行`FLUSHDB`和`FLUSHALL`命令，否则将导致源和目标的数据不一致。
-   如果目标库的数据逐出策略（`maxmemory-policy`）配置为`noeviction`以外的值，可能导致目标库的数据与源库不一致。关于数据逐出策略详情，请参见[Redis数据逐出策略介绍](/intl.zh-CN/用户指南/常见问题/ApsaraDB for Redis默认的数据逐出策略是什么？.md)。
-   如果源库中的某些Key使用了过期（expire）策略，由于可能存在Key已过期但未被及时删除的情况，所以在目标库中查看到的Key数量（例如通过info命令查看）会比源库的Key数量少。

    **说明：** 源和目标库中，未设置过期策略或未过期的Key数量是一致的。

-   如果目标Redis实例没有开通直连访问，DTS将采用代理转发模式将数据写入目标实例。

    **说明：** 关于直连访问的开通方法，请参见[开通直连访问](/intl.zh-CN/用户指南/管理实例/管理网络连接/开通直连访问.md)。


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

## 数据库账号的权限要求

|数据库|权限及授权方式|
|---|-------|
|源Redis实例|读写权限，关于授权方式，请参见[创建与管理账号](/intl.zh-CN/用户指南/账号与安全/创建与管理账号.md)。|
|目标Redis实例|

## 操作步骤

1.  购买数据同步作业，详情请参见[购买流程]()。

    **说明：** 购买时选择源实例为**Redis**、目标实例为**Redis**，并选择同步拓扑为**双向同步**。

2.  登录[数据传输控制台](https://dts-intl.console.aliyun.com/)。

3.  在左侧导航栏，单击**数据同步**。

4.  在同步作业列表页面顶部，选择同步的目标实例所属地域。

    ![选择地域](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p50604.png)

5.  配置正向同步作业。

    1.  定位至已购买的数据同步实例，单击该实例下第一个同步作业**操作**列的**配置同步链路**。

        **说明：** 一个双向数据同步实例会包含两个同步作业，需要分别进行配置。在配置反向同步作业时，定位至第二个同步作业，单击**操作**列的**配置同步链路**。

        ![双向同步任务](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1249459951/p41151.png)

    2.  配置同步作业的源实例及目标实例信息。

        ![配置源和目标实例信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1249459951/p98365.png)

        |类别|配置|说明|
        |:-|:-|:-|
        |无|同步作业名称|DTS会自动生成一个同步作业名称，建议配置具有业务意义的名称（无唯一性要求），便于后续识别。|
        |源实例信息|实例类型|选择**Redis实例**。|
        |实例地区|购买数据同步实例时选择的源实例地域，不可变更。|
        |实例ID|选择源Redis实例ID。 **说明：** 稍后配置反向同步作业时，此处需选择为正向同步作业里的目标Redis实例ID。 |
        |数据库密码|填入Redis实例的数据库账号密码。账号权限，请参见[数据库账号的权限要求](#section_rvb_h4w_l8r) 。**说明：** 数据库密码格式为<user\>:<password\>。例如，Redis实例自定义的用户名为admin，密码为Rp829dlwa，则此处填入的数据库密码为admin:Rp829dlwa。 |
        |目标实例信息|实例类型|选择**Redis实例**。|
        |实例地区|购买数据同步实例时选择的目标实例地域，不可变更。|
        |实例ID|选择目标Redis实例ID。 **说明：** 稍后配置反向同步作业时，此处需选择为正向同步作业里的源Redis实例ID。 |
        |数据库密码|填入Redis实例的数据库账号密码。 账号权限，请参见[数据库账号的权限要求](#section_rvb_h4w_l8r) 。**说明：** 数据库密码格式为<user\>:<password\>。例如，Redis实例自定义的用户名为admin，密码为Rp829dlwa，则此处填入的数据库密码为admin:Rp829dlwa。 |

    3.  单击页面右下角的**授权白名单并进入下一步**。

        DTS会将DTS服务器的IP地址会自动添加到源和目标Redis实例的白名单中，保障DTS服务器能够正常连接源和目标实例。

    4.  配置同步策略和同步对象。

        ![Redis双向同步选择对象](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1249459951/p98367.png)

        |类别|配置|说明|
        |:-|--|:-|
        |同步策略|冲突修复策略|**Overwrite**（遇到冲突，直接覆盖目标实例中的冲突记录） 当数据同步期间遇到Key相同但值不同时，Key的值更新时间晚的数据会覆盖冲突的记录。 |
        |目标已存在表的处理模式|        -   **预检查并报错拦截**：检查目标库是否为空。如果待同步的目标库为空，则通过该检查项目；如果不为空，则在预检查阶段提示错误，数据同步作业不会被启动。
        -   **忽略报错并继续执行**：跳过目标库是否为空的检查项。

**警告：** 选择为**忽略报错并继续执行**后，如果在同步初始化期间遇到目标库中的Key与源库中的Key相同，会将源库的数据覆盖写入目标库中，请谨慎选择。 |
        |选择同步对象|无|        -   在源库对象框中单击待同步的数据库，然后单击![向右箭头](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p40698.png)将其移动到已选择对象框。
        -   同步对象的选择粒度为库，暂不支持Key粒度的选择。 |

    5.  上述配置完成后，单击页面右下角的**下一步**。

    6.  配置同步初始化的选项。

        ![Redis双向同步初始化配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1249459951/p98370.png)

        当前固定为**包含全量数据+增量数据**，DTS会将源Redis实例中同步对象的存量数据初始化至目标Redis实例，并同步增量数据。

        **说明：**

        -   在配置反向同步作业时，如果同步对象已经初始化至目标实例，则直接同步增量数据。
        -   如果跳出版本相关的报错提示，请您按照提示将源Redis实例升级至指定版本。关于升级版本方式，请参见[升级大版本](/intl.zh-CN/用户指南/管理实例/管理生命周期/升级大版本.md)和[升级小版本](/intl.zh-CN/用户指南/管理实例/管理生命周期/升级小版本.md)。
    7.  单击页面右下角的**预检查并启动**。

        **说明：**

        -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
        -   如果预检查失败，单击具体检查项后的![提示](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p47468.png)图标，查看失败详情。根据提示修复后，重新进行预检查。
    8.  在预检查对话框中显示**预检查通过**后，关闭预检查对话框，正向同步作业将正式开始。

6.  等待正向同步作业完成初始化，直至处于**同步中**状态。

    您可以在数据同步页面，查看数据同步作业的状态。

7.  配置反向同步作业。

    1.  定位至第二个同步作业，单击**配置同步链路**。

        ![Redis配置反向同步作业](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1249459951/p98382.png)

    2.  重复[步骤5](#step_5mc_pl2_4i8)中的ii到viii配置步骤，完成反向同步作业的配置。


等待一段时间后，两个同步作业的链路状态均会处于**同步中**。

![Redis双向同步配置完成](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1249459951/p98389.png)

