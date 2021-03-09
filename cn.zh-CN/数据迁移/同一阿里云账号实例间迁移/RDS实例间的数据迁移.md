# RDS实例间的数据迁移

本文介绍如何使用数据传输服务（Data Transmission Service，简称DTS），实现RDS实例间的数据迁移。DTS支持结构迁移、全量数据迁移以及增量数据迁移，同时使用这三种迁移类型可以实现在自建应用不停服的情况下，平滑地完成数据库的迁移。

## 前提条件

迁移场景中RDS实例的数据库类型必须满足如下条件：

|源数据库|目标数据库|
|----|-----|
|RDS MySQL

RDS MariaDB

|RDS MySQL

RDS MariaDB |
|RDS SQL Server|RDS SQL Server|
|RDS PostgreSQL|RDS PostgreSQL|
|RDS PPAS|RDS PPAS|

## 注意事项

-   数据迁移不会影响源数据库的数据。数据迁移的实现原理是读取源库数据，并将数据复制至目标库，不会删除源库数据。更多迁移功能原理，请参见[数据迁移基本原理](/cn.zh-CN/产品简介/产品架构及功能原理.md)。
-   DTS在执行全量数据迁移时将占用源库和目标库一定的读写资源，可能会导致数据库的负载上升，在数据库性能较差、规格较低或业务量较大的情况下（例如源库有大量慢SQL、存在无主键表或目标库存在死锁等），可能会加重数据库压力，甚至导致数据库服务不可用。因此您需要在执行数据迁移前评估源库和目标库的性能，同时建议您在业务低峰期执行数据迁移（例如源库和目标库的CPU负载在30%以下）。
-   如果源库中待迁移的表没有主键或唯一约束，且所有字段没有唯一性，可能会导致目标数据库中出现重复数据。
-   当选择的迁移类型为全量数据迁移，那么为保障数据一致性，在迁移期间请勿在源RDS实例中写入新的数据。
-   对于迁移失败的任务，DTS会触发自动恢复。当您需要将业务切换至目标实例，请务必先结束或释放迁移任务，避免该任务被自动恢复后，使用源端数据覆盖目标实例的数据。
-   DTS会自动地在目标阿里云RDS实例中创建数据库，如果待迁移的数据库名称不符合阿里云RDS实例的定义规范，您需要在配置迁移任务之前在目标阿里云RDS实例中创建数据库。

    **说明：** 关于阿里云RDS的定义规范和创建数据库的操作方法，请参见[创建数据库和账号](/cn.zh-CN/RDS MySQL 数据库/快速入门/创建数据库和账号.md)。

-   如您需进行RDS PostgreSQL实例间迁移，由于业务切换到目标端后，新写入的Sequence不会按照源库的Sequence最大值作为初始值去递增，您需要在业务切换前，在源库中查询对应Sequence的最大值，然后在目标库中将其作为对应Sequence的初始值。查询源库Sequence值的相关命令如下：

    ```
    do language plpgsql $$
    declare
      nsp name;
      rel name;
      val int8;
    begin
      for nsp,rel in select nspname,relname from pg_class t2 , pg_namespace t3 where t2.relnamespace=t3.oid and t2.relkind='S'
      loop
        execute format($_$select last_value from %I.%I$_$, nsp, rel) into val;
        raise notice '%',
        format($_$select setval('%I.%I'::regclass, %s);$_$, nsp, rel, val+1);
      end loop;
    end;
    $$;
    ```


## 费用说明

|迁移类型|链路配置费用|公网流量费用|
|----|------|------|
|结构迁移和全量数据迁移|不收费。|通过公网将数据迁移出阿里云时将收费，详情请参见[产品定价](/cn.zh-CN/产品定价/产品定价.md)。|
|增量数据迁移|收费，详情请参见[产品定价](/cn.zh-CN/产品定价/产品定价.md)。|

## 迁移类型说明

-   结构迁移

    DTS将迁移对象的结构定义迁移到目标实例。

-   全量数据迁移

    DTS将源RDS实例迁移对象的存量数据，全部迁移到目标RDS实例中。

-   增量数据迁移

    DTS在全量迁移的基础上，将源RDS实例的增量更新数据同步到目标RDS实例中。通过增量数据迁移可以实现在自建应用不停服的情况下，平滑地完成数据迁移。


## 增量数据迁移阶段支持同步的SQL操作

|迁移场景|操作类型|SQL操作语句|
|----|----|-------|
|-   RDS MySQL间迁移
-   RDS MariaDB间迁移
-   RDS MariaDB与RDS MySQL间的迁移

|DML|INSERT、UPDATE、DELETE、REPLACE|
|DDL|-   ALTER TABLE、ALTER VIEW
-   CREATE FUNCTION、CREATE INDEX、CREATE PROCEDURE、CREATE TABLE、CREATE VIEW
-   DROP INDEX、DROP TABLE
-   RENAME TABLE
-   TRUNCATE TABLE |
|RDS SQL Server间迁移|DML|INSERT、UPDATE、DELETE**说明：** 不支持同步只更新大字段的UPDATE语句。 |
|DDL|-   ALTER TABLE，仅包含ADD COLUMN、DROP COLUMN、RENAME COLUMN
-   CREATE TABLE、CREATE INDEX

**说明：** CREATE TABLE不支持分区、表定义内部包含函数。

-   DROP TABLE
-   RENAME TABLE
-   TRUNCATE TABLE |
|RDS PostgreSQL间迁移

RDS PPAS间迁移

|DML|INSERT、UPDATE、DELETE|
|DDL|-   ALTER TABLE、ADD INDEX
-   CREATE TABLE、CREATE INDEX

**说明：** CREATE TABLE不支持分区表、表内定义包含函数的表。

-   DROP TABLE
-   RENAME TABLE |

## 数据库账号的权限要求

|迁移场景|数据库|结构迁移|全量迁移|增量迁移|
|----|:--|:---|:---|:---|
|-   RDS MySQL间迁移
-   RDS MariaDB间迁移
-   RDS MariaDB与RDS MySQL间的迁移

|源实例|SELECT权限|SELECT权限|REPLICATION CLIENT、REPLICATION SLAVE、SHOW VIEW和SELECT权限|
|目标实例|读写权限|读写权限|读写权限|
|RDS SQL Server间迁移|源实例|select权限|select权限|sysadmin权限|
|目标实例|读写权限|读写权限|读写权限|
|RDS PostgreSQL间迁移|源实例|pg\_catalog的usage权限|迁移对象的select权限|superuser**说明：** 如果RDS PostgreSQL实例没有superuser权限 |
|目标实例|迁移对象的create、usage权限|拥有授权数据库（owner）的操作权限，包括INSERT、UPDATE、DELETE。**说明：** RDS PostgreSQL的普通账号满足权限要求。

|拥有授权数据库（owner）的操作权限，包括INSERT、UPDATE、DELETE。**说明：** RDS PostgreSQL的普通账号满足权限要求。 |
|RDS PPAS间迁移|源实例|pg\_catalog的usage权限|迁移对象的select权限|superuser|
|目标实例|迁移对象的create、usage权限|schema的owner权限|schema的owner权限|

## 操作步骤

1.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据迁移**。
3.  在迁移任务列表页面顶部，选择迁移的目标实例所属地域。

    ![选择地域](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2767559951/p50439.png)

4.  单击页面右上角的**创建迁移任务**。
5.  配置迁移任务的源库及目标库信息。

    ![RDS实例间迁移](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2767559951/p49618.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |无|任务名称|DTS会自动生成一个任务名称，建议配置具有业务意义的名称（无唯一性要求），便于后续识别。|
    |源库信息|实例类型|选择**RDS实例**。|
    |实例地区|选择源RDS实例所属地域。|
    |RDS实例ID|选择源RDS实例ID。 **说明：** 源RDS和目标RDS实例可以不同或相同，即您可以使用DTS实现两个RDS实例间的数据迁移或同一RDS实例内的数据迁移。 |
    |数据库名称|填入RDS PostgreSQL实例中待迁移的数据库名。 **说明：** 当RDS实例的引擎为**PostgreSQL**时，该参数才会出现并需要配置。 |
    |数据库账号|填入源RDS实例的数据库账号，权限要求请参见[数据库账号的权限要求](#section_bjn_5zq_5hb)。|
    |数据库密码|填入该数据库账号对应的密码。 **说明：** 源库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的源库信息是否正确。源库信息填写正确则提示**测试通过**；如果提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的源库信息。 |
    |连接方式|根据需求选择**非加密连接**或**SSL安全连接**。如果设置为**SSL安全连接**，您需要提前开启RDS实例的SSL加密功能，详情请参见[设置SSL加密](https://help.aliyun.com/document_detail/96120.html)。 **说明：**

当RDS实例的引擎为**MySQL**时，该参数才会出现并需要配置。 |
    |目标库信息|实例类型|选择**RDS实例**。|
    |实例地区|选择目标RDS实例所属地域。|
    |RDS实例ID|选择目标RDS实例ID。 **说明：** 目标RDS实例和源RDS实例可以不同也可以相同，即您可以使用DTS实现两个RDS实例间的数据迁移或同一RDS实例内的数据迁移。 |
    |数据库名称|填入RDS PostgreSQL实例中待迁入数据的目标数据库名，可以和源实例中待迁移的数据库名不同。 **说明：** 当RDS实例的引擎为**PostgreSQL**时，该参数才会出现并需要配置。 |
    |数据库账号|填入目标RDS实例的数据库账号，权限要求请参见[数据库账号的权限要求](#section_bjn_5zq_5hb)。|
    |数据库密码|填入该数据库账号对应的密码。 **说明：** 目标库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的目标库信息是否正确。目标库信息填写正确则提示**测试通过**；如果提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的目标库信息。 |
    |连接方式|根据需求选择**非加密连接**或**SSL安全连接**。如果设置为**SSL安全连接**，您需要提前开启RDS实例的SSL加密功能，详情请参见[设置SSL加密](https://help.aliyun.com/document_detail/96120.html)。 **说明：** 当RDS实例的引擎为**MySQL**时，该参数才会出现并需要配置。 |

6.  配置完成后，单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到源和目标RDS实例的白名单中，用于保障DTS服务器能够正常连接RDS实例。

7.  选择迁移对象及迁移类型。

    ![选择迁移类型和迁移对象](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4944979951/p47745.png)

    |配置|说明|
    |:-|:-|
    |迁移类型|根据需求及各引擎对迁移类型的支持情况，选择迁移类型。

    -   如果只需要进行全量迁移，请同时勾选**结构迁移**和**全量数据迁移**。
    -   如果需要进行不停机迁移，请同时勾选**结构迁移**、**全量数据迁移**和**增量数据迁移**。
**说明：** 如果未选择**增量数据迁移**，为保障数据一致性，数据迁移期间请勿在源RDS实例中写入新的数据。 |
    |迁移对象|在迁移对象框中单击待迁移的对象，然后单击![向右小箭头](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p40698.png)将其移动到已选择对象框。

**说明：**

    -   迁移对象选择的粒度为库、表、列。若选择的迁移对象为表或列，其他对象（如视图、触发器、存储过程）不会被迁移至目标库。
    -   默认情况下，迁移对象在目标库中的名称与源库保持一致。如果您需要改变迁移对象在目标库中的名称，需要使用对象名映射功能，详情请参见[库表列映射](/cn.zh-CN/数据迁移/迁移任务管理/库表列映射.md)。
    -   如果使用了对象名映射功能，可能会导致依赖这个对象的其他对象迁移失败。 |

8.  单击页面右下角的**预检查并启动**。

    **说明：**

    -   在迁移任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动迁移任务。
    -   如果预检查失败，单击具体检查项后的![提示](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p47468.png)图标，查看失败详情。根据提示修复问题后，重新进行预检查。
9.  预检查通过后，单击**下一步**。
10. 在购买配置确认页面，选择**链路规格**并选中**数据传输（按量付费）服务条款**。
11. 单击**购买并启动**，迁移任务正式开始。
    -   全量数据迁移

        请勿手动结束迁移任务，否则可能导致数据不完整。您只需等待迁移任务完成即可，迁移任务会自动结束。

    -   增量数据迁移

        迁移任务不会自动结束，您需要手动结束迁移任务。

        **说明：** 请选择合适的时间手动结束迁移任务，例如业务低峰期或准备将业务切换至目标实例时。

        1.  观察迁移任务的进度变更为**增量迁移**，并显示为**无延迟**状态时，将源库停写几分钟，此时**增量迁移**的状态可能会显示延迟的时间。
        2.  等待迁移任务的**增量迁移**再次进入**无延迟**状态后，手动结束迁移任务。

            ![无延迟](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6767559951/p47604.png)


