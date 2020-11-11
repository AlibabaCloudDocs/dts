# 配置DTS与专线、VPN网关或智能接入网关间的路由

在配置数据迁移、数据同步或数据订阅时，如果源或目标库为通过专线、VPN网关或智能接入网关接入的自建数据库，您需要配置DTS与专线、VPN网关或智能接入网关间的路由，允许DTS访问该网络。

## 前提条件

自建数据库所属的本地网络已通过专线、VPN网关或智能接入网关的方式接入至阿里云。

**说明：** 相关接入方案请参见[本地IDC接入至阿里云方案概览](/cn.zh-CN/准备工作/本地IDC接入至阿里云/本地IDC接入至阿里云方案概览.md)，本文不做详细介绍。

## 配置DTS与云企业网间的路由

详情请参见[通过CEN实现本地IDC与DTS云服务互通](/cn.zh-CN/准备工作/本地IDC接入至阿里云/通过CEN实现本地IDC与DTS云服务互通.md)。

## 配置DTS与高速通道专线、智能接入网关间的路由

1.  登录[高速通道管理控制台](https://expressconnect.console.aliyun.com/)。
2.  在左侧导航栏，单击**物理专线连接** \> **边界路由器**。
3.  选择边界路由器的地域，然后单击目标边界路由器ID。
4.  单击**路由器条目**，然后单击**添加路由条目**。

    ![添加路由条目](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0855616951/p46180.png)

5.  配置路由条目，然后单击**确定**。

    |配置|说明|
    |:-|:-|
    |**目标网段**|填入待访问的DTS IP地址段。 **说明：** 关于如何选择需要访问的DTS IP地址段，请参见[迁移、同步或订阅本地数据库时需添加的IP白名单](/cn.zh-CN/准备工作/迁移、同步或订阅本地数据库时需添加的IP白名单.md)。 |
    |**下一跳类型**|选择为**专有网络**。 将目标网段的流量转发至选择的VPC。 |
    |**下一跳**|选择接收流量的下一跳实例。|

6.  将DTS的IP地址段作为**宣告BGP网段**，详情请参见[宣告BGP网段](https://help.aliyun.com/document_detail/91267.html#h2-url-5)。

## 配置DTS与VPN网关间的路由

1.  登录[专有网络控制台](https://vpc.console.aliyun.com)。
2.  在左侧导航栏单击**VPN** \> **IPsec连接**。
3.  编辑IPsec连接的配置信息，在本端网段参数中追加DTS的IP地址段并修改VPN连接版本为**ikev2**。

    **说明：**

    -   当VPN连接版本为**ikev1**版本时，只能配置一个本端网段，无法添加DTS的IP地址段，此处需要选择VPN连接版本为**ikev2**。
    -   关于如何选择需要访问的DTS IP地址段，请参见[迁移、同步或订阅本地数据库时需添加的IP白名单](/cn.zh-CN/准备工作/迁移、同步或订阅本地数据库时需添加的IP白名单.md)。
    ![调整IPsec配置](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4908225951/p46184.png)

4.  下载新的VPN配置并修改本地网关设备加载的VPN配置，详情请参见[在本地网关设备中加载VPN配置](https://help.aliyun.com/document_detail/65072.html#h2-url-5)。
5.  在本地网关设备中，添加一条静态路由：目的地址为[DTS服务器IP地址](/cn.zh-CN/准备工作/迁移、同步或订阅本地数据库时需添加的IP白名单.md)，下一跳为新增的IPsec-VPN隧道接口。

**说明：** 如果IPsec链接失败或网络不通，排查方法请参见[IPsec常见问题](https://help.aliyun.com/document_detail/65802.html)。

## 后续步骤

在配置数据迁移、数据同步或数据订阅时，选择实例类型为**通过专线/VPN网关/智能接入网关接入的自建数据库**，然后下拉选择和源库连通的VPC，即可将本地IDC中的自建数据库作为源库或目标库。相关配置案例请参见[DTS数据迁移方案概览](/cn.zh-CN/数据迁移/DTS数据迁移方案概览.md)或[DTS数据同步方案概览](/cn.zh-CN/数据同步/DTS数据同步方案概览.md)。

![选择专线接入](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/2908225951/p132742.png)

