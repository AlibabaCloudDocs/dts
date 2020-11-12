# 使用SDK示例代码消费订阅数据

完成数据订阅通道的配置后，您可以使用DTS提供的SDK示例代码来订阅数据变更信息，本文介绍该示例代码的使用说明。

已完成数据订阅通道（旧版）的配置，详情请参见[创建RDS MySQL数据订阅通道（旧版）](/cn.zh-CN/数据订阅/数据订阅（旧版）/创建RDS MySQL数据订阅通道（旧版）.md)或[创建DRDS数据订阅通道](/cn.zh-CN/数据订阅/数据订阅（旧版）/创建DRDS数据订阅通道.md)。

## 准备工作

创建AccessKey并获取AccessKey ID和AccessKey Secret信息，详情请参见[创建AccessKey](https://help.aliyun.com/document_detail/53045.html)。

**说明：** 如果使用子账号（RAM用户）来订阅数据，该账号需具备**AliyunDTSFullAccess**权限，授权方法请参见[通过系统策略授权子账号管理DTS](/cn.zh-CN/访问控制/通过系统策略授权子账号管理DTS.md)。

## 操作步骤

本文以IntelliJ IDEA软件（Community Edition 2018.1.4 Windows版本）为例，介绍如何运行SDK示例代码来消费订阅数据。

1.  根据订阅的数据源选择并下载SDK示例代码文件，然后解压该文件。

    -   RDS MySQL：[DtsSubscribeDemo](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/26647/cn_zh/1494853871968/DtsSubscribeDemo.zip)
    -   DRDS：[drds\_demo](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/120287/cn_zh/1605173283613/drds_demo.zip)
2.  定位至SDK示例代码解压的目录，使用文本编辑工具打开pom.xml文件，将数据订阅SDK的版本修改为最新版本。

    ![设置SDK版本](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9297248951/p99955.png)

    **说明：** 您可以在Maven网站中获取最新的数据订阅SDK版本，详情请参见[数据订阅SDK的Maven页面](https://search.maven.org/artifact/com.aliyun.dts/dts-subscribe-sdk)。

3.  打开IntelliJ IDEA软件，然后单击**Open**。

    ![打开项目](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9297248951/p88391.png)

4.  在弹出的对话框中，定位至SDK示例代码解压的目录，依次展开文件夹，找到项目对象模型文件：pom.xml。

    ![找到项目对象模型文件](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9297248951/p99824.png)

5.  在弹出对话框中，选择**Open as Project**。

6.  在IntelliJ IDEA软件界面，依次展开文件夹，找到并双击打开对应的Java客户端文件。

    -   RDS MySQL：对应的文件为Main.java。

        ![打开RDS MySQL的java客户端文件](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0397248951/p99840.png)

    -   DRDS：对应的文件为drds\_demo.java。

        ![打开DRDS的java客户端文件](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0397248951/p99841.png)

7.  设置Java客户端文件中的代码参数。

    ![设置参数](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0397248951/p99912.png)

    |参数|说明|
    |--|--|
    |accessKey|分别填写阿里云账号的AccessKey ID和AccessKey Secret信息，详情请参见[准备工作](#section_ytc_6yk_pv1)。|
    |accessSecret|
    |subscribeInstanceID|填写数据订阅实例ID。您可以登录[数据传输控制台](https://dts.console.aliyun.com/)，单击左侧导航栏的**数据订阅**，然后找到目标数据订阅实例ID。|

8.  在IntelliJ IDEA软件界面的顶部，选择**Run** \> **Run**运行该客户端。

    **说明：** 首次运行时，软件将自动加载相关依赖包并完成安装。


运行结果如下图所示，该客户端可正常订阅到源库的数据变更信息。

![SDK示例代码运行结果](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0397248951/p99946.png)

