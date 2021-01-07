# 跨阿里云账号迁移或同步专有网络下的自建数据库时如何配置RAM授权

通过本文介绍的方法配置RAM授权后，DTS可以在配置数据迁移或同步时读取其他阿里云账号下的专有网络，您可以将其他阿里云账号下通过专线接入的自建数据库迁移或同步至当前云账号下的云数据库中。

专线所属的阿里云账号已经授权DTS的RAM角色访问其云资源，详情请参见[授予DTS访问云资源的权限](/cn.zh-CN/访问控制/授予DTS访问云资源的权限.md)。

本地IDC或第三方云已通过专线、VPN网关或智能接入网关接入至阿里云，现在需要将本地IDC或第三方云中的自建数据库通过专有网络迁移或同步至其他阿里云账号下的云数据库中，详细架构如下图所示。

**说明：** 在使用DTS跨阿里云账号迁移或同步专有网络下的自建数据库之前，您需要在专线所属的阿里云账号中配置RAM授权，同时将目标实例所属的云账号设置为授信云账号，然后授权其访问专线所属的阿里云账号相关云资源。

![背景信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4787549951/p66757.png)

## 步骤一：创建RAM角色并授予其DTS默认角色权限

1.  使用专线所属的阿里云账号登录[RAM控制台](https://ram.console.aliyun.com/)。

2.  在左侧导航栏，单击**RAM角色管理**。

3.  单击**新建RAM角色**，选择可信实体类型为**阿里云账号**，单击**下一步**。

4.  在新建RAM角色对话框，配置RAM角色信息。

    ![配置RAM角色信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5454884951/p44837.png)

    |配置选项|配置说明|
    |:---|:---|
    |角色名称|填写RAM角色名称，本案例填写**ram-for-dts**。 **说明：** 可填写大写字母、小写字母、数字或短横线（-），长度不超过64个字符。 |
    |备注（可选）|填写RAM角色备注信息。|
    |选择云账号|选择为**其他云账号**，填写目标实例所属的云账号ID作为授信云账号。 **说明：** 您需要使用目标实例所属的云账号登录[账号管理](https://account.console.aliyun.com/#/secure)页面来获取云账号ID。

![获取云账号ID](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8149459951/p44838.png) |

5.  单击**完成**。

6.  单击**精确授权**。

7.  在添加权限对话框中，选择权限类型为**系统策略**，然后输入策略名称：**AliyunDTSRolePolicy**。

    ![配置权限策略](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5454884951/p44840.png)

8.  单击**确定**。

9.  单击**关闭**。


## 步骤二：授权RAM角色访问阿里云账号下的专有网络

1.  使用专线所属的阿里云账号登录[RAM控制台](https://ram.console.aliyun.com/)。

2.  在左侧导航栏，单击**RAM角色管理**。

3.  找到在[步骤一](#section_bsu_lje_o5s)中创建的RAM角色，单击对应的RAM角色名称。

    ![单击RAM角色名称](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6454884951/p66567.png)

4.  在RAM角色的基本信息页面，单击添加权限。

5.  在添加权限对话框中，输入策略名称**AliyunVPCReadOnlyAccess**进行搜索，然后单击该策略名称将其移动至**已选择**区域框。

    ![配置权限策略](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6454884951/p66568.png)

6.  单击**确定**。

7.  在RAM角色的基本信息页面，单击信任策略管理页签。

8.  单击**修改信任策略**，将下述代码复制至策略框中。

    ![修改信任策略](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6454884951/p67506.png)

    ```
    {
        "Statement": [
            {
                "Action": "sts:AssumeRole",
                "Effect": "Allow",
                "Principal": {
                    "RAM": [
                        "acs:ram::目标实例所属的云账号ID:root"
                    ],
                    "Service": [
                        "目标实例所属的云账号ID@dts.aliyuncs.com"
                    ]
                }
            }
        ],
        "Version": "1"
    }
    ```

    **说明：** 您需要使用目标实例所属的云账号登录[账号管理](https://account.console.aliyun.com/#/secure)页面来获取云账号ID，并替换至上述代码中的`目标实例所属的云账号ID`。

    ![获取云账号ID](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8149459951/p44838.png)


## 配置案例

-   [从通过专线接入的自建MySQL迁移至其他账号下的RDS MySQL](/cn.zh-CN/数据迁移/从自建数据库迁移至阿里云/源库为MySQL/从通过专线接入的自建MySQL迁移至其他账号下的RDS MySQL.md)
-   [跨云账号同步Redis集群版实例](/cn.zh-CN/数据同步/Redis数据同步/跨云账号同步Redis集群版实例.md)

