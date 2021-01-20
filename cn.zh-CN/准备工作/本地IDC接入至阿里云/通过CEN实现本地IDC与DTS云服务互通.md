# 通过CEN实现本地IDC与DTS云服务互通

在配置数据迁移或数据同步时，如果源或目标库通过专线或智能接入网关接入至云企业网的自建数据库，您需在自建数据库所属的本地IDC配置访问DTS云服务的路由，实现DTS与自建数据库间的互通。

![DTS与CEN关系图](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0855616951/p104577.png)

将VBR或CCN加载到同一个云企业网CEN（Cloud Enterprise Network）实例后，您只需要在自建数据库所属的本地IDC配置访问DTS云服务的路由，即可实现本地IDC的网络与DTS云服务的互通。在后续配置数据迁移或数据同步时，即可直接将通过VBR或CCN接入的本地网络环境下的自建数据库作为源库或目标库，实现便捷、安全的数据迁移上云。

## 操作步骤

1.  将自建数据库的本地网络所属的[边界路由器](https://help.aliyun.com/document_detail/44854.html)VBR（Virtual border router）或[云连接网](https://help.aliyun.com/document_detail/93667.html)CCN（Cloud Connect Network）加载到同一个云企业网实例，详情请参见[加载网络实例]()。

2.  在云企业网控制台设置云服务访问，详情请参见[设置云服务访问](~~106671~~)。

    **说明：** 您可以跳过本步骤，在配置DTS的数据迁移或数据同步任务时，DTS会自动完成云服务访问的配置。

3.  选择下述方法，添加本地IDC访问DTS云服务的路由。

    -   通过宣告BGP让VBR自动学习路由信息。
        1.  登录[高速通道管理控制台](https://expressconnect.console.aliyun.com/)。
        2.  在左侧导航栏，单击**物理专线连接** \> **边界路由器**。
        3.  选择边界路由器的地域，然后单击目标边界路由器ID。
        4.  单击**路由器条目**，然后单击**添加路由条目**。

            ![添加路由条目](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0855616951/p46180.png)

        5.  配置路由条目，然后单击**确定**。

            |配置|说明|
            |:-|:-|
            |**目标网段**|填入待访问的DTS IP地址段。 **说明：** 关于如何选择需要访问的DTS IP地址段，请参见[迁移、同步或订阅本地数据库时需添加的IP白名单](/cn.zh-CN/准备工作/迁移、同步或订阅本地数据库时需添加的IP白名单.md)。 |
            |**下一跳类型**|选择为**专有网络**。 将目标网段的流量转发至选择的VPC。 |
            |**下一跳**|选择接收流量的下一跳实例。 **说明：** 如果已经配置了到其他云服务的路由，为避免网段重叠导致访问异常，您需要选择与其相同的下一跳。 |

        6.  将DTS的IP地址段作为**宣告BGP网段**，详情请参见[宣告BGP网段](https://help.aliyun.com/document_detail/91267.html#h2-url-5)。
    -   在本地IDC网络中手动添加访问DTS云服务的静态路由。

        **说明：** 关于如何选择需要访问的DTS IP地址段，请参见[迁移、同步或订阅本地数据库时需添加的IP白名单](/cn.zh-CN/准备工作/迁移、同步或订阅本地数据库时需添加的IP白名单.md)。


在配置数据迁移或数据同步时，选择实例类型为**通过云企业网CEN接入的自建数据库**，然后下拉选择对应的云企业网实例ID和VPC，即可将本地IDC中的自建数据库作为源库或目标库。配置流程请参见[DTS数据迁移方案概览](/cn.zh-CN/数据迁移/DTS数据迁移方案概览.md)或[DTS数据同步方案概览](/cn.zh-CN/数据同步/DTS数据同步方案概览.md)。

![选择通过云企业网CEN接入](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0855616951/p104665.png)

