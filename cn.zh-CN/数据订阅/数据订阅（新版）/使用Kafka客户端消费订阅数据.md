# 使用Kafka客户端消费订阅数据

新版数据订阅支持使用0.11版本至2.0版本的Kafka客户端消费订阅数据，DTS为您提供了Kafka客户端Demo，本文将介绍该客户端的使用说明。

## 注意事项

-   使用本文提供的Demo消费数据时，如果采用auto commit（自动提交），可能会因为数据还没被消费完就执行了提交操作，从而丢失部分数据，建议采用手动提交的方式以避免该问题。

    **说明：** 如果发生故障没有提交成功，重启客户端后会从上一个记录的位点进行数据消费，期间会有部分重复数据，您需要手动过滤。

-   数据以Avro序列化存储，详细格式请参见[Record.avsc](https://github.com/LioRoger/subscribe_example/blob/master/avro/Record.avsc)文档。

    **警告：** 如果您使用的不是本文提供的Kafka客户端，在进行反序列化解析时，可能出现解析的数据有误，您需要自行验证数据的正确性。

-   关于`offsetFotTimes`接口，DTS的搜索单位为秒，原生Kafka的搜索单位为毫秒。

## Kafka客户端运行流程说明

请下载[Kafka客户端Demo代码](https://github.com/LioRoger/subscribe_example)。更多关于代码使用的详细介绍，请参见Demo中的[Readme](https://github.com/LioRoger/subscribe_example/blob/master/javaimpl/Readme)文档。

**说明：** 如需使用Kafka客户端2.0版本，您需要修改subscribe\_example-master/javaimpl/pom.xml文件，将kafka客户端的版本号修改成2.0.0。

![kafka2.0](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5698322061/p171738.png)

|步骤|相关目录或文件|
|--|-------|
|1、使用原生的Kafka consumer从数据订阅通道中获取增量数据。|subscribe\_example-master/javaimpl/src/main/java/recordgenerator/|
|2、将获取的增量数据镜像执行反序列化，并从中获取 、 和其他属性。|subscribe\_example-master/javaimpl/src/main/java/boot/MysqlRecordPrinter.java|
|3、将反序列化后的数据中的dataTypeNumber字段转换为对应的MySQL/Oracle字段类型。 **说明：** 关于对应关系的详情，请参见文末的[MySQL字段类型与dataTypeNumber数值的对应关系](#section_woc_4pq_mes)和[Oracle字段类型与dataTypeNumber数值的对应关系](#section_u8e_rjz_2ss)。

|subscribe\_example-master/javaimpl/src/main/java/recordprocessor/mysql/|

## 操作步骤

本文以IntelliJ IDEA软件（Community Edition 2018.1.4 Windows版本）为例，介绍如何运行该客户端消费订阅通道中的数据。****

1.  创建新版数据订阅通道，详情请参见[创建RDS MySQL数据订阅通道（新版）](/cn.zh-CN/数据订阅/数据订阅（新版）/创建RDS MySQL数据订阅通道（新版）.md)、[创建PolarDB MySQL数据订阅通道](/cn.zh-CN/数据订阅/数据订阅（新版）/创建PolarDB MySQL数据订阅通道.md)或[创建Oracle数据订阅通道](/cn.zh-CN/数据订阅/数据订阅（新版）/创建Oracle数据订阅通道.md)。

2.  创建一个或多个消费组，详情请参见[新增消费组](/cn.zh-CN/数据订阅/数据订阅（新版）/新增消费组.md)。

3.  下载[Kafka客户端Demo代码](https://github.com/LioRoger/subscribe_example)，然后解压该文件。

4.  打开IntelliJ IDEA软件，然后单击**Open**。

    ![打开项目](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9297248951/p88391.png)

5.  在弹出的对话框中，定位至Kafka客户端Demo代码下载的目录，参照下图依次展开文件夹，找到项目对象模型文件：pom.xml。

    ![打开项目文件](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9197248951/p88398.png)

6.  在弹出对话框中，选择**Open as Project**。

7.  在IntelliJ IDEA软件界面，依次展开文件夹，找到并双击打开Kafka客户端Demo文件：NotifyDemo.java。

    ![打开kafka客户端demo文件](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1508298951/p88407.png)

8.  设置NotifyDemo.java文件中的各参数对应的值。

    ![设置参数值](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0297248951/p88411.png)

    |参数|说明|获取方式|
    |:-|:-|----|
    |USER\_NAME|消费组的账号。 **警告：** 如您未使用本文提供的客户端，请按照`<消费组的账号>-<消费组ID>`的格式设置用户名（例如：`dtstest-dtsae******bpv`），否则无法正常连接。

|在DTS控制台单击目标订阅实例ID，然后单击数据消费，您可以获取到**消费组ID**和消费组的**账号**信息。 **说明：** 消费组账号的密码已在您新建消费组时指定。

![查看消费组和账号](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0297248951/p88419.png) |
    |PASSWORD\_NAME|该账号的密码。|
    |SID\_NAME|消费组ID。|
    |GROUP\_NAME|消费组名称，需保持和消费组ID相同（即本参数也填入消费组ID）。|
    |KAFKA\_TOPIC|数据订阅通道的订阅Topic。|在DTS控制台单击目标订阅实例ID，在订阅配置页面，您可以获取到**订阅Topic**、网络地址及端口号信息。![获取topic和网络信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0297248951/p48804.png) |
    |KAFKA\_BROKER\_URL\_NAME|数据订阅通道的网络地址及端口号信息。 **说明：** 如果您部署Kafka客户端所属的ECS实例与数据订阅通道属于经典网络或同一专有网络，建议通过内网地址进行数据订阅，网络延迟最小。 |
    |INITIAL\_CHECKPOINT\_NAME|消费的数据时间点，格式为Unix时间戳，例如1592269238。 **说明：** 您需要自行保存时间点信息，以便：

    -   当业务程序中断后，传入已消费的数据时间点继续消费数据，防止数据丢失。
    -   在订阅客户端启动时，传入所需的消费位点，调整订阅位点，实现按需消费数据。
|消费的数据时间点必须在订阅实例的数据范围（如下图所示）之内，并需转化为Unix时间戳。![数据范围](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8435978061/p127325.png)

**说明：**

    -   数据范围查看方式，请参见[查看订阅数据](/cn.zh-CN/数据订阅/数据订阅（新版）/查看订阅数据.md)。
    -   Unix时间戳转换工具可以用搜索引擎获取。 |
    |USE\_CONFIG\_CHECKPOINT\_NAME|默认取值为true，即强制使用指定的数据时间点来消费数据，避免丢失已接收到的但未处理的数据。|无|
    |SUBSCRIBE\_MODE\_NAME|一个消费组下支持同时启动两个Kafka客户端实现灾备，如需实现该功能，请部署两个客户端并将该参数的值设置为subscribe。默认值为assign，即不使用该功能，只部署一个客户端。

|无|

9.  在IntelliJ IDEA软件界面的顶部，选择**Run** \> **Run**运行该客户端。

    **说明：** 首次运行时，软件将自动加载相关依赖包并完成安装。




## 执行结果

运行结果如下图所示，该客户端可正常订阅到源库的数据变更信息。

![Kafka客户端订阅结果](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1508298951/p88599.png)

您也可以去除NotifyDemo.java文件中的打印日志详情的注释（即删除第25行`//log.info(ret);`中的`//`），然后再次运行该客户端即可查看详细的数据变更信息。

![kafka客户端运行的详细结果](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1508298951/p88601.png)

## 常见问题

-   Q：为什么需要自行记录客户端的消费位点？

    A：由于DTS记录的消费位点是接收到Kafka消费客户端执行commit操作的时间点，可能与当前实际消费到的时间点存在一定的时间差。当业务程序或Kafka消费客户端异常中断后，您可以传入自行记录的消费位点以继续消费，避免消费到重复的数据或缺失部分数据。


## MySQL字段类型与dataTypeNumber数值的对应关系

|MySQL字段类型|对应dataTypeNumber数值|
|:--------|:-----------------|
|MYSQL\_TYPE\_DECIMAL|0|
|MYSQL\_TYPE\_INT8|1|
|MYSQL\_TYPE\_INT16|2|
|MYSQL\_TYPE\_INT32|3|
|MYSQL\_TYPE\_FLOAT|4|
|MYSQL\_TYPE\_DOUBLE|5|
|MYSQL\_TYPE\_NULL|6|
|MYSQL\_TYPE\_TIMESTAMP|7|
|MYSQL\_TYPE\_INT64|8|
|MYSQL\_TYPE\_INT24|9|
|MYSQL\_TYPE\_DATE|10|
|MYSQL\_TYPE\_TIME|11|
|MYSQL\_TYPE\_DATETIME|12|
|MYSQL\_TYPE\_YEAR|13|
|MYSQL\_TYPE\_DATE\_NEW|14|
|MYSQL\_TYPE\_VARCHAR|15|
|MYSQL\_TYPE\_BIT|16|
|MYSQL\_TYPE\_TIMESTAMP\_NEW|17|
|MYSQL\_TYPE\_DATETIME\_NEW|18|
|MYSQL\_TYPE\_TIME\_NEW|19|
|MYSQL\_TYPE\_JSON|245|
|MYSQL\_TYPE\_DECIMAL\_NEW|246|
|MYSQL\_TYPE\_ENUM|247|
|MYSQL\_TYPE\_SET|248|
|MYSQL\_TYPE\_TINY\_BLOB|249|
|MYSQL\_TYPE\_MEDIUM\_BLOB|250|
|MYSQL\_TYPE\_LONG\_BLOB|251|
|MYSQL\_TYPE\_BLOB|252|
|MYSQL\_TYPE\_VAR\_STRING|253|
|MYSQL\_TYPE\_STRING|254|
|MYSQL\_TYPE\_GEOMETRY|255|

## Oracle字段类型与dataTypeNumber数值的对应关系

|Oracle字段类型|对应dataTypeNumber数值|
|:---------|:-----------------|
|VARCHAR2/NVARCHAR2|1|
|NUMBER/FLOAT|2|
|LONG|8|
|DATE|12|
|RAW|23|
|LONG\_RAW|24|
|UNDEFINED|29|
|XMLTYPE|58|
|ROWID|69|
|CHAR、NCHAR|96|
|BINARY\_FLOAT|100|
|BINARY\_DOUBLE|101|
|CLOB/NCLOB|112|
|BLOB|113|
|BFILE|114|
|TIMESTAMP|180|
|TIMESTAMP\_WITH\_TIME\_ZONE|181|
|INTERVAL\_YEAR\_TO\_MONTH|182|
|INTERVAL\_DAY\_TO\_SECOND|183|
|UROWID|208|
|TIMESTAMP\_WITH\_LOCAL\_TIME\_ZONE|231|

