# 跨云账号同步Redis集群版实例

数据传输服务DTS（Data Transmission Service）支持跨云账号单向同步Redis集群版实例（简称为Redis实例），适用于云账号间的资源迁移或合并、业务架构调整等多种应用场景。

-   源Redis实例的引擎版本为4.0（社区版）或5.0（社区版、企业版）。

    **说明：** 目标Redis实例支持的版本为4.0或5.0版本，如需跨版本同步（仅支持从低版本同步到高版本）请提前确认兼容性。例如创建按量付费的Redis实例来测试，测试完成后可将该实例释放或转为包年包月。

-   源Redis实例的网络类型为专有网络。如果当前为经典网络，您可以切换网络类型，详情请参见[切换为专有网络](/intl.zh-CN/用户指南/管理实例/管理网络连接/切换为专有网络.md)。
-   源Redis实例的SSL加密功能需处于关闭状态，详情请参见[设置SSL加密](/intl.zh-CN/用户指南/账号与安全/设置SSL加密.md)。
-   目标Redis实例可用的存储空间需大于源Redis实例已使用的存储空间。
-   由于Redis企业版扩展了[Redis模块]()（Redis Modules），如果源Redis实例为企业版，为保障业务兼容性，要求目标Redis实例为Redis企业版。

现有两个Redis实例分别属于不同的阿里云账号，由于业务需求，需要将云账号A下的Redis实例中的业务数据信息迁移至云账号B下的Redis实例中，详细架构如下图所示。

![Redis跨账号迁移架构图](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8149459951/p132220.png)

**说明：** 跨云账号同步Redis实例仅支持集群架构或标准架构，暂不支持读写分离架构。

数据迁移方案介绍：

DTS的数据迁移功能暂不支持迁移Redis实例（集群版），本方案采用DTS的数据同步功能完成数据的迁移。在本方案中，您需要经历如下步骤以完成同步作业的配置。

|步骤|说明|
|--|--|
|1、使用云账号A登录阿里云控制台，完成RAM角色授权操作，详情请参见[准备工作](#section_b4m_p6z_fi6)中的步骤1。|配置RAM角色，将目标实例所属的云账号（即云账号B）设置为授信云账号，并授权其访问本账号的云资源。|
|2、使用云账号A登录阿里云控制台，为源Redis实例执行一些准备工作，详情请参见[准备工作](#section_b4m_p6z_fi6)中的步骤2~4。|将DTS服务器的IP地址添加至源Redis的白名单中，然后为源Redis实例申请直连访问地址并创建具备**复制**权限的账号。**说明：** 由于Redis集群版实例暂不支持创建**复制**权限的账号，您需要[提交工单](https://workorder-intl.console.aliyun.com/?#/ticket/add/?productId=1226)申请开通该功能。 |
|3、使用云账号B登录阿里云控制台，完成数据同步作业配置，详情请参见[操作步骤](#section_hck_cyj_2o4)。|由于DTS暂不直接支持跨云账号读取源Redis实例的信息，在配置数据同步作业时，您需要将源实例作为其他阿里云账号下通过专线接入的自建数据库来完成配置。|

## 注意事项

-   DTS在执行全量数据初始化时将占用源库和目标库一定的资源，可能会导致数据库服务器负载上升。如果数据库业务量较大或服务器规格较低，可能会加重数据库压力，甚至导致数据库服务不可用。建议您在执行数据同步前谨慎评估，并在业务低峰期执行数据同步。
-   数据同步期间，请勿在源库中执行`FLUSHDB`和`FLUSHALL`命令，否则将导致源和目标的数据不一致。
-   如果目标库的数据逐出策略（`maxmemory-policy`）配置为`noeviction`以外的值，可能导致目标库的数据与源库不一致。关于数据逐出策略详情，请参见[Redis数据逐出策略介绍](/intl.zh-CN/用户指南/常见问题/ApsaraDB for Redis默认的数据逐出策略是什么？.md)。
-   如果源库中的某些Key使用了过期（expire）策略，由于可能存在Key已过期但未被及时删除的情况，所以在目标库中查看到的Key数量（例如通过info命令查看）会比源库的Key数量少。

    **说明：** 源和目标库中，未设置过期策略或未过期的Key数量是一致的。


## 支持同步的操作命令

|版本类型|操作命令|
|----|----|
|云数据库Redis社区版|-   APPEND
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
-   SWAPDB、UNLINK（仅当源端Redis实例的版本为4.0时支持） |
|云数据库Redis企业版|-   APPEND
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
-   UNLINK、ZADD、ZINCRBY、ZINTERSTORE、ZREM、ZREMRANGEBYLEX、ZUNIONSTORE、ZREMRANGEBYRANK、ZREMRANGEBYSCORE |

**说明：**

-   对于通过EVAL或者EVALSHA调用Lua脚本，在增量数据同步时，由于目标端在执行脚本时不会明确返回执行结果，DTS无法确保该类型脚本能够执行成功。
-   对于List，由于DTS在调用sync或psync进行重传时，不会对目标端已有的数据进行清空，可能导致出现重复数据。

## 准备工作

使用源Redis实例所属的阿里云账号登录[阿里云控制台](https://homenew.console.aliyun.com/)，完成下述准备工作。

1.  创建RAM角色并授权其访问本云账号下的相关云资源，详情请参见[跨阿里云账号迁移或同步专有网络下的自建数据库时如何配置RAM授权](/intl.zh-CN/RAM授权管理/跨阿里云账号迁移或同步专有网络下的自建数据库时如何配置RAM授权.md)。

2.  为源Redis实例申请直连访问地址，详情请参见[开通直连访问](/intl.zh-CN/用户指南/管理实例/管理网络连接/开通直连访问.md)。

3.  根据源Redis实例所属的地域选择DTS服务器的IP地址段，然后将这些IP地址段加入至Redis实例的白名单中，操作方法请参见[设置IP白名单](/intl.zh-CN/快速入门/步骤2：设置白名单.md)。

    **说明：** 关于各地域下DTS服务器的IP地址段信息，请参见[迁移、同步或订阅本地数据库时需添加的IP白名单]()。

4.  为源Redis实例创建用于数据同步的账号。

    1.  提交工单申请，由阿里云售后团队为您的集群版实例开通创建复制权限账号的功能。

        **说明：** 由于Redis集群版实例暂不支持创建**复制**权限的账号，您需要[提交工单](https://workorder-intl.console.aliyun.com/?#/ticket/add/?productId=1226)申请开通该功能。

    2.  创建具备**复制**权限的账号，详情请参见[创建与管理账号](/intl.zh-CN/用户指南/账号与安全/创建与管理账号.md)。


## 操作步骤

1.  使用目标Redis实例所属的阿里云账号登录[阿里云控制台](https://homenew.console.aliyun.com/)，然后购买数据同步作业，详情请参见[购买流程]()。

    **说明：** 购买时，关键参数限制如下：

    -   选择源实例为**Redis**、目标实例为**Redis**，并选择同步拓扑为**单向同步**。
    -   根据源和目标Redis实例所属的地域，选择对应的地域。
2.  登录[数据传输控制台](https://dts-intl.console.aliyun.com/)。

3.  在左侧导航栏，单击**数据同步**。

4.  在同步作业列表页面顶部，选择同步的目标实例所属地域。

    ![选择地域](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p50604.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。

6.  配置数据同步的源和目标实例信息。

    1.  配置具有业务意义的数据同步作业名称（无唯一性要求），便于后续识别。

    2.  选择实例类型为**通过专线/VPN网关/智能接入网关接入的自建数据库**，然后单击**其他阿里云账号下的专有网络**。

        **说明：** 由于DTS暂不直接支持跨云账号读取源Redis实例的信息，在配置数据同步作业时，您需要将源实例作为其他阿里云账号下通过专线接入的自建数据库来完成配置。

        ![选择为其他云账号下的专有网络](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8149459951/p132244.gif)

    3.  配置源实例信息。

        ![配置源实例信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8149459951/p132253.png)

        |配置|说明|
        |:-|:-|
        |实例类型|选择**通过专线/VPN网关/智能接入网关接入的自建数据库**。|
        |实例地区|购买数据同步实例时选择的源实例地域信息，不可变更。|
        |所属阿里云账号ID|填入源Redis实例所属的阿里云账号ID。 **说明：** 您可以使用源Redis实例所属的阿里云账号登录[账号管理](https://account.console.aliyun.com/#/secure)页面来获取云账号ID。

![获取云账号ID](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8149459951/p44838.png) |
        |角色名称|填入[准备工作](#section_b4m_p6z_fi6)的步骤1中创建的RAM角色名称。|
        |已和源端数据库联通的VPC|选择源Redis实例所属的专有网络。|
        |数据库类型|选择**Redis**。|
        |实例模式|选择**集群版**。|
        |IP地址|填入[准备工作](#section_b4m_p6z_fi6)的步骤2中，为源Redis实例申请的直连访问地址对应的IP地址，本案例中填入192.168.0.153。 **说明：** 您可以在电脑中通过Ping命令来获取源Redis实例直连访问地址对应的IP地址，例如`ping r-********.redis.rds.aliyuncs.com`。 |
        |端口|填入源Redis实例的服务端口。|
        |数据库密码|填入[准备工作](#section_b4m_p6z_fi6)的步骤4中，为源Redis实例创建的具备**复制**权限的账号密码。**说明：** 数据库密码格式为<user\>:<password\>。例如，Redis实例自定义的用户名为admin，密码为Rp829dlwa，则此处填入的数据库密码为admin:Rp829dlwa。 |

    4.  配置目标实例信息。

        ![配置目标实例信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8149459951/p132254.png)

        |配置|说明|
        |:-|:-|
        |实例类型|选择**Redis实例**。|
        |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
        |实例ID|选择目标Redis实例ID。|
        |数据库密码|填入目标Redis实例的数据库密码，需具备**读写**权限。**说明：** 数据库密码格式为<user\>:<password\>。例如，Redis实例自定义的用户名为admin，密码为Rp829dlwa，则此处填入的数据库密码为admin:Rp829dlwa。 |

7.  单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址，自动添加到目标Redis实例的白名单中，用于保障DTS服务器能够正常连接源和目标实例。

8.  配置目标已存在表的处理模式和同步对象。

    ![配置同步信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p71478.png)

    |配置|说明|
    |:-|:-|
    |目标已存在表的处理模式|    -   **预检查并报错拦截**：检查目标库是否为空。如果待同步的目标库为空，则通过该检查项目；如果不为空，则在预检查阶段提示错误，数据同步作业不会被启动。
    -   **忽略报错并继续执行**：跳过目标库是否为空的检查项。

**警告：** 选择为**忽略报错并继续执行**后，如果在同步过程中遇到目标库中的Key与源库中的Key相同，会将源库的数据覆盖写入目标库中，请谨慎选择。 |
    |同步对象|    -   在源库对象框中单击待同步的数据库，然后单击![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p40698.png)将其移动到已选择对象框。
    -   同步对象的选择粒度为库，暂不支持Key粒度的选择。 |

9.  上述配置完成后，单击页面右下角的**下一步**。

10. 配置同步初始化的选项。

    ![Redis同步初始化](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7349459951/p50606.png)

    **说明：**

    -   当前固定为**包含全量数据+增量数据**，即DTS会将源实例中的存量数据同步至目标Redis数据库中，并同步增量数据。
    -   如果跳出版本相关的报错提示，请您按照提示将源Redis实例升级至指定版本。关于升级版本方式，请参见[升级大版本](/intl.zh-CN/用户指南/管理实例/管理生命周期/升级大版本.md)和[升级小版本](/intl.zh-CN/用户指南/管理实例/管理生命周期/升级小版本.md)。
11. 上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：**

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![提示](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p47468.png)图标，查看失败详情。根据提示修复后，重新进行预检查。
12. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，同步作业将正式开始。

13. 等待同步作业的链路初始化完成，直至处于**同步中**状态。

    ![数据同步状态](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1349459951/p41059.png)

    **说明：** 您可以在**数据同步**页面，查看数据同步作业的状态。


