# 通过CEN实现本地IDC与DTS云服务互通

在配置数据迁移或数据同步时，如果源或目标库通过专线或智能接入网关接入至云企业网的自建数据库，您需在自建数据库所属的本地IDC配置访问DTS云服务的路由，实现DTS与自建数据库间的互通。

![DTS与CEN关系图](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0855616951/p104577.png)

将VBR或CCN加载到同一个云企业网CEN（Cloud Enterprise Network）实例后，您只需要在自建数据库所属的本地IDC配置访问DTS云服务的路由，即可实现本地IDC的网络与DTS云服务的互通。在后续配置数据迁移或数据同步时，即可直接将通过VBR或CCN接入的本地网络环境下的自建数据库作为源库或目标库，实现便捷、安全的数据迁移上云。

## 操作步骤

1.  将自建数据库的本地网络所属的[边界路由器](https://help.aliyun.com/document_detail/44854.html)VBR（Virtual border router）或[云连接网](https://help.aliyun.com/document_detail/93667.html)CCN（Cloud Connect Network）加载到同一个云企业网实例，详情请参见[加载网络实例]()。

2.  在云企业网控制台设置云服务访问，详情请参见[设置云服务访问](~~106671~~)。

    **说明：** 您可以跳过本步骤，在配置DTS的数据迁移或数据同步任务时，DTS会自动完成云服务访问的配置。


在配置数据迁移或数据同步时，选择实例类型为**通过云企业网CEN接入的自建数据库**，然后下拉选择对应的云企业网实例ID和VPC，即可将本地IDC中的自建数据库作为源库或目标库。配置流程请参见[DTS数据迁移方案概览](/cn.zh-CN/数据迁移/DTS数据迁移方案概览.md)或[DTS数据同步方案概览](/cn.zh-CN/数据同步/DTS数据同步方案概览.md)。

![选择通过云企业网CEN接入](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0855616951/p104665.png)

