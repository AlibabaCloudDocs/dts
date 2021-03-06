# 从自建Oracle迁移至PolarDB-O集群

PolarDB是阿里云自研的下一代关系型云数据库，有三个独立的引擎，分别可以100%兼容MySQL、100%兼容PostgreSQL、高度兼容Oracle语法，适用于企业多样化的数据库应用场景。通过使用数据传输服务DTS（Data Transmission Service），您可以将自建Oracle迁移至PolarDB-O集群。

-   自建Oracle数据库的版本为9i、10g、11g、12c、18c或19c版本。
-   自建Oracle数据库已开启ARCHIVELOG（归档模式），设置合理的归档日志保持周期且归档日志能够被访问，详情请参见[ARCHIVELOG](https://docs.oracle.com/database/121/ADMIN/archredo.htm#ADMIN008)。
-   自建Oracle数据库已开启Supplemental Logging，且已开启supplemental\_log\_data\_pk，supplemental\_log\_data\_ui，详情请参见[Supplemental Logging](https://docs.oracle.com/database/121/SUTIL/GUID-D857AF96-AC24-4CA1-B620-8EA3DF30D72E.htm#SUTIL1582)。
-   自建Oracle数据库中的待迁移表需具备主键或非空唯一索引。

## 注意事项

-   DTS在执行全量数据迁移时将占用源库和目标库一定的读写资源，可能会导致数据库的负载上升，在数据库性能较差、规格较低或业务量较大的情况下（例如源库有大量慢SQL、存在无主键表或目标库存在死锁等），可能会加重数据库压力，甚至导致数据库服务不可用。因此您需要在执行数据迁移前评估源库和目标库的性能，同时建议您在业务低峰期执行数据迁移（例如源库和目标库的CPU负载在30%以下）。
-   对于迁移失败的任务，DTS会触发自动恢复。在您将业务切换至目标库之前，请务必先停止或释放该任务，避免该任务被自动恢复，导致源端数据覆盖目标库的数据。
-   如果您的自建Oracle版本为12c及以上，待迁移表的名称长度需不超过30个字节。

## 费用说明

|迁移类型|链路配置费用|公网流量费用|
|----|------|------|
|结构迁移和全量数据迁移|不收费。|通过公网将数据迁移出阿里云时将收费，详情请参见[产品定价]()。|
|增量数据迁移|收费，详情请参见[产品定价]()。|

## 迁移类型说明

|迁移类型|说明|
|----|--|
|结构迁移|DTS将迁移对象的结构定义迁移到目标PolarDB-O集群（简称PolarDB集群）。目前DTS支持的对象包括：表、视图、同义词、触发器、存储过程、存储函数、包、自定义类型。 **说明：** 不兼容触发器。当迁移对象包含了触发器可能导致数据不一致。 |
|全量数据迁移|DTS会将自建Oracle数据库迁移对象的存量数据，全部迁移至PolarDB集群。 **说明：** 在结构迁移和全量数据迁移完成之前，请勿对迁移对象执行DDL操作，否则可能导致迁移失败。 |
|增量数据迁移|DTS在全量数据迁移的基础上轮询并捕获自建Oracle数据库产生的redo log，将自建Oracle数据库的增量更新数据同步到目标PolarDB集群中。目前支持仅支持同步DML操作（INSERT、UPDATE、DELETE），暂不支持同步DDL操作。通过增量数据迁移可以实现在自建应用不停服的情况下，平滑地完成Oracle数据库迁移至PolarDB集群。 |

## 准备工作

登录待迁移的Oracle数据库，创建用于采集数据的账号并授权。

**说明：** 如您已创建包含下述权限的账号，可跳过本步骤。

|数据库|结构迁移|全量迁移|增量数据迁移|
|:--|:---|:---|:-----|
|自建Oracle数据库|schema的owner权限|schema的owner权限|DBA|
|PolarDB集群|schema的owner权限|schema的owner权限|schema的owner权限|

数据库账号创建及授权方法：

-   自建Oracle数据库请参见[CREATE USER](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_8003.htm)和[GRANT](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_9013.htm)。
-   PolarDB集群请参见[创建账号](~~118194~~)。

**说明：** 如需执行增量数据迁移，且不允许授予DBA权限，您可以参照以下内容为数据库账号授予更精细化的权限。



## 操作步骤

1.  登录[数据传输控制台](https://dts-intl.console.aliyun.com/)。

2.  在左侧导航栏，单击**数据迁移**。

3.  在迁移任务列表页面顶部，选择迁移的目标集群所属地域。

    ![选择地域](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2767559951/p50439.png)

4.  单击页面右上角的**创建迁移任务**。

5.  配置迁移任务的源库和目标库连接信息。

    ![配置源库和目标库连接信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9337214061/p68785.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |无|任务名称|DTS会自动生成一个任务名称，建议配置具有业务意义的名称（无唯一性要求），便于后续识别。|
    |源库信息|实例类型|根据源库的部署位置进行选择，本文以**有公网IP的自建数据库**为例介绍配置流程。 **说明：** 当自建数据库为其他实例类型时，您还需要执行相应的准备工作，详情请参见[准备工作概览]()。 |
    |实例地区|当实例类型选择为**有公网IP的自建数据库**时，**实例地区**无需设置。 **说明：** 如果您的自建Oracle数据库进行了白名单安全设置，您需要在**实例地区**配置项后，单击**获取DTS IP段**来获取到DTS服务器的IP地址，并将获取到的IP地址加入自建Oracle数据库的白名单安全设置中。 |
    |数据库类型|选择**Oracle**。|
    |主机名或IP地址|填入自建Oracle数据库的访问地址，此处填入公网IP地址。|
    |端口|填入自建Oracle数据库的服务端口，默认为**1521**。 **说明：** 本案例中，该服务端口已开放至公网。 |
    |实例类型|    -   **非RAC实例**：选择该项后，您还需要填写**SID**信息。
    -   **RAC实例**：选择该项后，您还需要填写**ServiceName**信息。 |
    |数据库账号|填入自建Oracle的数据库账号，权限要求请参见[准备工作](#section_m4e_lp6_p1o)。|
    |数据库密码|填入该数据库账号对应的密码。 **说明：** 源库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的源库信息是否正确。源库信息填写正确则提示**测试通过**；如果提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的源库信息。 |
    |目标库信息|实例类型|选择**PolarDB**。|
    |实例地区|选择目标PolarDB集群所属地域。|
    |PolarDB实例ID|选择目标PolarDB集群ID。|
    |数据库名称|填入待迁入数据的数据库名称。|
    |数据库账号|填入目标PolarDB集群的数据库账号，权限要求请参见[准备工作](#section_m4e_lp6_p1o)。|
    |数据库密码|填入该数据库账号对应的密码。 **说明：** 目标库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的目标库信息是否正确。目标库信息填写正确则提示**测试通过**；如果提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的目标库信息。 |

6.  配置完成后，单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到目标PolarDB集群的白名单中，用于保障DTS服务器能够正常连接目标PolarDB集群。

7.  选择迁移类型和迁移对象。

    ![选择迁移类型和迁移对象](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8887549951/p68786.png)

    |配置|说明|
    |:-|:-|
    |迁移类型|    -   如果只需要进行全量迁移，则同时勾选**结构迁移**和**全量数据迁移**。
    -   如果需要进行不停机迁移，则同时勾选**结构迁移**、**全量数据迁移**和**增量数据迁移**。
**说明：**

    -   如果未勾选**增量数据迁移**，为保障数据一致性，数据迁移期间请勿在源库中写入新的数据。
    -   如果勾选**增量数据迁移**，数据迁移期间，DTS会增量迁移无主键表的ROWID列，可用于无主键表的数据去重和数据校验。 |
    |迁移对象|在迁移对象框中单击待迁移的对象，然后单击![向右小箭头](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p40698.png)将其移动至已选择对象框。

**说明：**

    -   迁移对象选择的粒度为库、表、列。
    -   默认情况下，迁移对象在目标库中的名称与源库保持一致。如果您需要改变迁移对象在目标库中的名称，需要使用对象名映射功能，详情请参见[库表列映射](/intl.zh-CN/数据迁移/迁移任务管理/库表列映射.md)。
    -   如果使用了对象名映射功能，可能会导致依赖这个对象的其他对象迁移失败。 |

8.  单击页面右下角的**预检查并启动**。

    **说明：**

    -   在迁移任务正式启动之前，会先进行预检查。只有通过预检查，DTS才能迁移数据。
    -   如果预检查失败，单击具体检查项后的![提示](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p47468.png)图标，查看失败详情。根据提示修复后，重新进行预检查。
9.  预检查通过后，单击**下一步**。

10. 在弹出的购买配置确认对话框，选择**链路规格**并选中**数据传输（按量付费）服务条款**。

11. 单击**购买并启动**，迁移任务正式开始。

    -   结构迁移+全量数据迁移

        请勿手动结束迁移任务，否则可能会导致数据不完整。您只需等待迁移任务完成即可，迁移任务会自动结束。

    -   结构迁移+全量数据迁移+增量数据迁移

        迁移任务不会自动结束，您需要手动结束迁移任务。

        **说明：** 请选择合适的时间手动结束迁移任务，例如业务低峰期或准备将业务切换至目标集群时。

        1.  观察迁移任务的进度变更为**增量迁移**，并显示为**无延迟**状态时，将源库停写几分钟，此时**增量迁移**的状态可能会显示延迟的时间。
        2.  等待迁移任务的**增量迁移**再次进入**无延迟**状态后，手动结束迁移任务。

            ![结束增量迁移任务](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6767559951/p47604.png)

12. 将业务切换至PolarDB集群。


