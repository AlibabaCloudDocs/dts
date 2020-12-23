# 跨阿里云账号数据迁移或同步时如何配置RAM授权

DTS支持将另一个阿里云账号下的RDS实例数据迁移或同步至当前阿里云账号，本文将介绍在跨阿里云账号使用DTS进行数据迁移或同步时，源实例所属的阿里云账号如何配置RAM授权。

## 前提条件

源实例所属的阿里云账号已经授权DTS的RAM角色访问其云资源，详情请参见[授予DTS访问云资源的权限](/intl.zh-CN/RAM授权管理/授予DTS访问云资源的权限.md)。

## DTS跨账号数据迁移或同步支持的实例类型

|支持的功能|源实例|目标实例|
|:----|:--|:---|
|数据迁移|RDS实例|RDS实例|
|DRDS实例|
|HybridDB for MySQL实例|
|OceanBase实例|
|ECS自建数据库|
|有公网IP的自建数据库|
|数据同步|RDS实例|RDS实例|
|MaxCompute（原ODPS）实例|
|Elasticsearch实例|

## 背景信息

在使用DTS进行数据迁移或者数据同步时，需要在源实例所属云账号中配置RAM授权，将目标实例所属云账号（主账号）作为授信云账号，允许其通过数据传输服务访问源实例所属云账号的相关云资源。

**说明：** 授权操作完成后，请在目标实例所属的云账号（主账号）中创建数据迁移或同步任务。

## 操作步骤

1.  使用源实例所属云账号登录[RAM控制台](https://ram.console.aliyun.com/)。
2.  在左侧导航栏，单击**RAM角色管理**。
3.  单击**创建RAM角色**，选择可信实体类型为**阿里云账号**，单击**下一步**。
4.  在弹出的对话框中，配置RAM角色信息。

    ![配置RAM角色信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5454884951/p44837.png)

    |配置选项|配置说明|
    |:---|:---|
    |角色名称|填写RAM角色名称，本案例填写**ram-for-dts**。 **说明：** 可以填写大写英文、小写英文、数字或短横线（-），长度不超过64个字符。 |
    |备注（可选）|填写RAM角色备注信息。|
    |选择云账号|选择为**其他云账号**，填写目标实例所属的云账号ID（主账号）作为授信云账号。 **说明：** 您需要使用目标实例所属的云账号（主账号）登录[账号管理](https://account.console.aliyun.com/#/secure)页面来获取云账号ID。

![获取云账号ID](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8149459951/p44838.png) |

5.  单击**完成**。
6.  单击**精确授权**。

    ![单击精确授权](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3717571951/p120781.png)

7.  在添加权限对话框中选择权限类型为**系统策略**，并输入策略名称：**AliyunDTSRolePolicy**。

    ![配置权限策略](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5454884951/p44840.png)

8.  单击**确定**。
9.  单击**关闭**。
10. 在**RAM角色管理**页面，找到刚刚创建的RAM角色，单击对应的RAM角色名称。

    ![单击RAM角色名称](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6454884951/p66567.png)

11. 在RAM角色的基本信息页面，单击信任策略管理页签。
12. 在信任策略管理页签，单击**修改信任策略**，将下述代码复制至策略框中。

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

    **说明：** 您需要使用目标实例所属的云账号（主账号）登录[账号管理](https://account.console.aliyun.com/#/secure)页面来获取云账号ID，并替换至上述代码中的`目标实例所属的云账号ID`。

    ![获取云账号ID](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8149459951/p44838.png)


完成权限授权后，即可配置跨云账号数据迁移或同步任务。

## 下一步

使用目标实例所属的阿里云账号登录[数据传输控制台](https://dts-intl.console.aliyun.com/)，创建数据迁移或同步任务。

**说明：** 相关配置案例请参见[不同阿里云账号下RDS MySQL实例间的数据同步](/intl.zh-CN/数据同步/MySQL间数据同步/不同阿里云账号下RDS MySQL实例间的数据同步.md)。

