# 从自建Oracle迁移至PolarDB-O集群（迁移结构）

本文介绍如何使用[ADAM](https://help.aliyun.com/document_detail/53703.html)将自建Oracle数据库的结构迁移至PolarDB-O集群，然后使用DTS进行数据迁移。

-   已通过ADAM专业版或企业版使用权限的申请，详情请参见[申请ADAM专业版或企业版](https://page.aliyun.com/form/act1157826644/index.htm)。
-   自建Oracle数据库的版本为9i、10g、11g、12c、18c或19c版本。
-   自建Oracle数据库已开启ARCHIVELOG（归档模式），设置合理的归档日志保持周期且归档日志能够被访问，详情请参见[ARCHIVELOG](https://docs.oracle.com/database/121/ADMIN/archredo.htm#ADMIN008)。
-   自建Oracle数据库已开启Supplemental Logging，且已开启supplemental\_log\_data\_pk，supplemental\_log\_data\_ui，详情请参见[Supplemental Logging](https://docs.oracle.com/database/121/SUTIL/GUID-D857AF96-AC24-4CA1-B620-8EA3DF30D72E.htm#SUTIL1582)。
-   自建Oracle数据库中的待迁移表需具备主键或非空唯一索引。
-   如果您的自建Oracle版本为12c及以上，待迁移表的名称长度需不超过30个字节。
-   PolarDB-O集群（简称PolarDB集群）的`polar_comp_redwood_raw_names`参数已设置为off，详情请参见[t217468.md\#]()。

## 费用说明

ADAM专业版或企业版需要申请通过才可以使用（目前免费），您可以自行[申请](https://page.aliyun.com/form/act1157826644/index.htm)。

**说明：** 您也可以使用[数据库专家服务](https://help.aliyun.com/document_detail/118910.html)，由阿里云数据库专家为您提供应用架构和数据库架构治理、评估选型、制定数据库平滑迁云迁移方案，费用说明请参见[计费方式与价格](https://help.aliyun.com/document_detail/119643.html)。

## 步骤一：采集数据库结构信息

ADAM目前提供下述两种采集方式供您选择。

|采集方式|适用场景|文档链接|
|----|----|----|
|在线数据库采集|源库可以连通公网且已开放服务端口至公网。|[在线采集]()|
|部署采集器采集|源库无法连通公网。|[下载采集器并采集数据]()|

## 步骤二：分析数据库画像

1.  登录[ADAM控制台](https://adam.console.aliyun.com/)，单击左侧导航栏的**数据库迁云** \> **数据库评估**。

2.  在**数据库采集**页签的底部，单击**下一步查看源库画像**。

3.  单击**新建画像**，设置画像的基本信息并单击**创建**。

    |配置|说明|
    |--|--|
    |**画像名**|为画像配置具有业务意义的名称便于后续识别。|
    |**类型**|固定为**Oracle**。|
    |**报告语言类型**|根据需求选择为**中文**或**英文**。|
    |**数据文件**|    1.  单击**上传**按钮。
    2.  选择在[步骤一：采集数据库结构信息](#section_bcr_isw_miy)中采集结果所输出的压缩包文件。 |

4.  返回画像列表，画像分析完成后，您可以单击**操作**列的**详情**，从数据库的规模、会话、风险、热点、复杂度、负载等维度分析评估数据库的具体信息。

    ![分析画像](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5887549951/p77002.png)

    **说明：**

    -   更多关于数据库画像的分析解读，请参见[数据库画像分析](https://help.aliyun.com/document_detail/160298.html)。
    -   分析完成后，您还可以单击页面下方的**下一步查看目标库选型建议**，了解迁移到各种目标库的兼容情况分析，详情请参见[目标库选型建议](https://help.aliyun.com/document_detail/160301.html)。

## 步骤三：目标库兼容性评估

1.  在ADAM控制台，单击左侧导航栏的**数据库迁云** \> **数据库评估**。

2.  单击**目标库兼容评估**页签。

3.  单击**新建项目**，设置项目的基本信息并单击**创建**。

    |配置|说明|
    |--|--|
    |**项目名**|为项目配置具有业务意义的名称便于后续识别。|
    |**项目类型**|选择为**Oracle\_To\_POLARDB O**。|
    |**目标库版本**|选择为**POLARDB O**。|
    |**报告语言**|根据需求选择为**中文**或**英文**。|
    |**源库画像**|选择在[步骤二](#section_ynl_0kh_t4o)中创建的源库画像。|
    |**LOB数据迁移到OSS**|是否需要将LOB（Large Object）数据迁移到对象存储服务OSS（Object Storage Service）中，根据需求选择为**是**或**否**。|
    |**选择评估Schema**|根据业务需求，选择待评估的Schema信息。     1.  在**未选择**区域框中，勾选待评估的Schema。
    2.  单击![向右箭头adam](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5887549951/p77004.png)将其添加至**已选择**区域框。 |

    该项目将创建成功并进入**处理中**状态。

4.  等待项目评估完成，单击**操作**列的**详情**，全面评估目标库的兼容性、规格与迁移风险，了解ADAM提供相应的解决方案。

    **说明：** 更多关于评估结果的分析解读，请参见[数据库评估](https://help.aliyun.com/document_detail/60035.html)。您也可以使用[数据库专家服务](https://help.aliyun.com/document_detail/118910.html)，由阿里云数据库专家为您提供应用架构和数据库架构治理、评估选型、制定数据库平滑迁云迁移方案。


## 步骤四：数据库改造迁移

|改造类型|准备工作|具体步骤|
|----|----|----|
|线下改造迁移|需自行部署ADAM STUDIO工具。|详情请参见[线下改造迁移](https://help.aliyun.com/document_detail/160082.html)。|
|线上改造迁移|-   为PolarDB集群申请公网连接地址。
-   将ADAM服务器的IP地址段（39.100.131.0/24,47.241.17.0/24）添加至PolarDB集群的白名单，详情请参见[设置集群白名单](https://help.aliyun.com/document_detail/118183.html)。

|详情请参见[线上改造迁移](https://help.aliyun.com/document_detail/160081.html)。|

**警告：** 线上或线下改造迁移仅执行结构迁移，在迁移过程中可评估相关的提示信息，然后执行结构订正以获得更好的兼容性。



[从自建Oracle迁移至PolarDB-O集群（迁移数据）](/cn.zh-CN/数据迁移/从自建数据库迁移至阿里云/源库为Oracle/从自建Oracle迁移至PolarDB-O集群（迁移数据）.md)

