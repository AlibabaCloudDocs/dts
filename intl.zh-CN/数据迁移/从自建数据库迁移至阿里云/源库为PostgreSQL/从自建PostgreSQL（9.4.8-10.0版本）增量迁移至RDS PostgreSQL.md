# 从自建PostgreSQL（9.4.8-10.0版本）增量迁移至RDS PostgreSQL

本文介绍如何使用数据传输服务DTS（Data Transmission Service），将自建PostgreSQL增量迁移至RDS PostgreSQL实例。DTS支持结构迁移、全量数据迁移和增量数据迁移，同时使用这三种迁移类型可以实现在自建应用不停服的情况下，平滑地完成自建PostgreSQL数据库迁移上云。

本文以**有公网IP的自建数据库**为例介绍增量数据迁移的配置流程。如您仅需要全量数据迁移，请参见[从自建PostgreSQL全量迁移至RDS PostgreSQL](/intl.zh-CN/数据迁移/从自建数据库迁移至阿里云/源库为PostgreSQL/从自建PostgreSQL全量迁移至RDS PostgreSQL.md)。

## 前提条件

-   自建PostgreSQL数据库版本为9.4.8及以上、9.5、9.6或10.0版本。
-   RDS PostgreSQL实例的存储空间须大于自建PostgreSQL数据库占用的存储空间。
-   自建PostgreSQL数据库的服务端口已开放至公网。

## 注意事项

-   DTS在执行全量数据迁移时将占用源库和目标库一定的读写资源，可能会导致数据库的负载上升，在数据库性能较差、规格较低或业务量较大的情况下（例如源库有大量慢SQL、存在无主键表或目标库存在死锁等），可能会加重数据库压力，甚至导致数据库服务不可用。因此您需要在执行数据迁移前评估源库和目标库的性能，同时建议您在业务低峰期执行数据迁移（例如源库和目标库的CPU负载在30%以下）。
-   在增量数据迁移过程中，如果迁移对象的选择粒度为Schema，在待迁移的Schema中创建了新的表或使用RENAME命令重建了待迁移的表，您需要在对该表写入数据前执行`ALTER TABLE schema.table REPLICA IDENTITY FULL;`命令。

    **说明：** 将上述命令中的`schema`和`table`替换成真实的Schema名和表名。

-   如果源库中待迁移的表没有主键或唯一约束，且所有字段没有唯一性，可能会导致目标数据库中出现重复数据。
-   对于迁移失败的任务，DTS会触发自动恢复。当您需要将业务切换至目标实例，请务必先终止或释放该任务，避免该任务被自动恢复后，导致源端数据覆盖目标实例的数据。

## 迁移限制

-   一个数据迁移任务只能对一个数据库进行数据迁移，如果有多个数据库需要迁移，则需要为每个数据库创建数据迁移任务。
-   待迁移的数据库名称中间不能包含短划线（-），例如dts-testdata。
-   如果迁移过程中源库发生了主备切换，DTS的增量数据迁移无法实现断点续传。
-   由于源库的主备节点可能存在同步延迟导致数据不一致，执行数据迁移时请使用源库的主节点作为迁移的数据源。

    **说明：** 为避免数据迁移对业务的影响，请在业务低峰期执行数据迁移，您还可以根据源库的读写压力情况调整迁移速率，详情请参见[调整全量迁移速率](/intl.zh-CN/数据迁移/迁移任务管理/调整全量迁移速率.md)。

-   DTS的校验对象为数据内容，暂不支持Sequence等元数据的校验，您需要自行校验。
-   由于业务切换到目标端后，新写入的Sequence不会按照源库的Sequence最大值作为初始值去递增，您需要在业务切换前，在源库中查询对应Sequence的最大值，然后在目标库中将其作为对应Sequence的初始值。查询源库Sequence值的相关命令如下：

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

-   增量数据迁移阶段仅支持DML操作（INSERT、DELETE、UPDATE）的同步。

    **说明：** 仅2020年10月1日后创建的数据迁移任务支持同步DDL操作，您需要在配置迁移任务前，在源库中创建触发器和函数来捕获DDL信息，详情请参见[通过触发器和函数实现PostgreSQL的DDL增量迁移](/intl.zh-CN/最佳实践/通过触发器和函数实现PostgreSQL的DDL增量迁移.md)。


## 费用说明

|迁移类型|链路配置费用|公网流量费用|
|----|------|------|
|结构迁移和全量数据迁移|不收费。|通过公网将数据迁移出阿里云时将收费，详情请参见[产品定价]()。|
|增量数据迁移|收费，详情请参见[产品定价]()。|

## 数据库账号的权限要求

|数据库|结构迁移|全量迁移|增量迁移|
|:--|:---|:---|:---|
|自建PostgreSQL数据库|pg\_catalog的usage权限|迁移对象的select权限|superuser|
|RDS PostgreSQL实例|迁移对象的create、usage权限|schema的owner权限|schema的owner权限|

数据库账号创建及授权方法：

-   自建PostgreSQL数据库请参见[CREATE USER](https://www.postgresql.org/docs/10/sql-createuser.html)和[GRANT](https://www.postgresql.org/docs/10/sql-grant.html)语法。
-   RDS PostgreSQL实例请参见[创建账号](https://help.aliyun.com/document_detail/96753.html)[创建账号](https://www.alibabacloud.com/help/zh/doc-detail/96753.htm)。

## 数据迁移流程说明

为解决对象间的依赖，提高迁移成功率，DTS对PostgreSQL结构及数据的迁移顺序如下表所示。

**说明：** 关于结构迁移、全量数据迁移、增量数据迁移的术语介绍，请参见[名词解释]()。

|DTS自动执行的迁移流程|迁移说明|
|------------|----|
|1.结构迁移|DTS迁移TABLE、VIEW、SEQUENCE、FUNCTION、USER DEFINED TYPE、RULE、DOMAIN、OPERATION、AGGREGATE的结构信息至目标库。 **说明：** 不支持迁移插件、使用C语言编写的FUNCTION。 |
|2.全量数据迁移|DTS将迁移对象的存量数据全部迁移至目标库。|
|3.结构迁移|DTS迁移TRIGGER、FOREIGN KEY的结构信息至目标库。|
|4.增量数据迁移|在全量数据迁移的基础上，DTS将迁移对象的增量更新迁移至目标库。在增量数据迁移阶段，DTS支持同步的SQL语句如下：

-   DML：INSERT、UPDATE、DELETE
-   DDL：

    -   CREATE TABLE、DROP TABLE
    -   ALTER TABLE（包括RENAME TABLE、ADD COLUMN、ADD COLUMN DEFAULT、ALTER COLUMN TYPE、DROP COLUMN、ADD CONSTRAINT、ADD CONSTRAINT CHECK、ALTER COLUMN DROP DEFAULT）
    -   CREATE INDEX ON TABLE、DROP INDEX
    -   DROP RULE
    -   CREATE SEQUENCE、ALTER SEQUENCE RENAME TO、DROP SEQUENCE
**说明：** 您也可以在配置迁移任务前，在源库中创建触发器和函数来捕获DDL信息，详情请参见[通过触发器和函数实现PostgreSQL的DDL增量迁移](/intl.zh-CN/最佳实践/通过触发器和函数实现PostgreSQL的DDL增量迁移.md)。仅2020年10月1日后创建的数据迁移任务支持通过该操作实现同步DDL操作。


通过增量数据迁移可以实现在自建应用不停服的情况下，平滑地完成数据迁移。**说明：** 增量数据迁移阶段不支持迁移bit类型的数据。

**说明：**

-   增量数据迁移阶段仅支持DML操作（INSERT、DELETE、UPDATE）的同步。
-   增量数据迁移阶段不支持迁移bit类型的数据。 |

## 准备工作一：安装逻辑流复制插件及调整设置

在配置增量数据迁移任务之前，需要在自建PostgreSQL中安装DTS提供的逻辑流复制插件并调整系统设置。

**说明：** 本案例以PostgreSQL9.6版本为例进行演示。

1.  在自建PostgreSQL服务器上，使用`wget`命令下载和自建PostgreSQL版本对应的逻辑流复制插件。下载地址如下：
    -   [适用于PostgreSQL9.4版本的插件](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/120287/cn_zh/1558945296691/ali_decoding_9.4.tar)
    -   [适用于PostgreSQL9.5版本的插件](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/120287/cn_zh/1559539318467/ali_decoding_9.5.tar)
    -   [适用于PostgreSQL9.6版本的插件](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/120287/cn_zh/1596087544655/ali_decoding_build9.6.tar)
    -   [适用于PostgreSQL10.0版本的插件](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/109395/cn_zh/1594017403595/ali_decoding_10.tar)
2.  将下载的插件进行解压操作。

    ```
    tar xvf ali_decoding_9.6.tar
    ```

3.  将ali\_decoding.so复制至PostgreSQL安装路径中的lib目录中。

    ```
    cp ali_decoding.so /usr/lib/postgresql/9.6/lib/
    ```

4.  将ali\_decoding.control复制至PostgreSQL安装路径下的extension目录中。

    ```
    cp ali_decoding.control /usr/share/postgresql/9.6/extension/
    ```

5.  使用具有superuser权限的账号登录自建PostgreSQL数据库。
6.  将`max_replication_slots`设置为大于1的整数，本案例设置为`5`。

    **说明：** 详情请参见[PostgreSQL官方文档](https://www.postgresql.org/docs/current/logical-replication-config.html)。

    ```
    ALTER SYSTEM set max_replication_slots = '5';
    ```

7.  将`wal_level`设置为`logical`。

    ```
    ALTER SYSTEM SET wal_level = logical;
    ```

8.  将`max_wal_senders`设置为大于1的整数，本案例设置为`5`。

    **说明：** max\_wal\_senders 参数用于设置可以运行的并发任务数，建议和`max_replication_slots`设置的值一致。

    ```
    ALTER SYSTEM SET max_wal_senders = '5';
    ```

9.  返回至自建PostgreSQL服务器的Shell命令窗口，执行以下命令重启PostgreSQL服务。

    ```
    service postgresql restart
    ```

10. 重新登录自建PostgreSQL数据库，执行以下命令查看是否能正常创建复制槽。

    ```
    SELECT * FROM pg_create_logical_replication_slot('replication_slot_test', 'ali_decoding');
    ```

    如果返回信息如下，则代表逻辑流复制插件已经安装成功。

    ![测试插件是否正常安装](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4097549951/p47988.png)

11. 将DTS的IP地址加入至PostgreSQL的配置文件（pg\_hba.conf）中，关于该配置文件的设置请参见[pg\_hba.conf文件](https://www.postgresql.org/docs/current/auth-pg-hba-conf.html)。如果您已将地址配置为`0.0.0.0/0`，可跳过本步骤。

    **说明：** 您只需添加目标数据库所在区域对应的DTS IP地址段，详情请参见[迁移、同步或订阅本地数据库时需添加的IP白名单]()。


## 准备工作二：在目标实例中创建数据库和schema

根据待迁移对象所属的数据库和schema信息，在目标RDS PostgreSQL中创建相应数据库和schema（schema名称须一致），详情请参见[创建数据库](https://help.aliyun.com/document_detail/96758.html)[创建数据库](https://www.alibabacloud.com/help/zh/doc-detail/96758.htm)和[schema管理](https://help.aliyun.com/document_detail/26159.html)[schema管理](https://www.alibabacloud.com/help/zh/doc-detail/26159.htm)。

## 操作步骤

1.  登录[数据传输控制台](https://dts.console.aliyun.com/)[数据传输控制台](https://dts-intl.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据迁移**。
3.  在迁移任务列表页面顶部，选择迁移的目标实例所属地域。

    ![选择地域](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2767559951/p50439.png)

4.  单击页面右上角的**创建迁移任务**。
5.  配置迁移任务的源库及目标库信息。

    ![源库和目标库连接配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0932559951/p47957.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |无|任务名称|DTS会自动生成一个任务名称，建议配置具有业务意义的名称（无唯一性要求），便于后续识别。|
    |源库信息|实例类型|根据源库的部署位置进行选择，本文以**有公网IP的自建数据库**为例介绍配置流程。 **说明：** 当自建数据库为其他实例类型时，您还需要执行相应的准备工作，详情请参见[准备工作概览]()。 |
    |实例地区|当实例类型选择为**有公网IP的自建数据库**时，**实例地区**无需设置。 **说明：** 如果您的自建PostgreSQL数据库进行了白名单安全设置，您需要在**实例地区**配置项后，单击**获取DTS IP段**来获取到DTS服务器的IP地址，并将获取到的IP地址加入自建PostgreSQL数据库的白名单安全设置中。 |
    |数据库类型|选择**PostgreSQL**。|
    |主机名或IP地址|填入自建PostgreSQL数据库的访问地址，本案例中填入公网地址。|
    |端口|填入自建PostgreSQL数据库的服务端口，默认为**5432**。|
    |数据库名称|填入自建PostgreSQL数据库中待迁移的数据库名。|
    |数据库账号|填入自建PostgreSQL的数据库账号，权限要求请参见[数据库账号的权限要求](#section_577_f3j_wg1)。|
    |数据库密码|填入该数据库账号对应的密码。 **说明：** 源库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的源库信息是否正确。源库信息填写正确则提示**测试通过**；如果提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的源库信息。 |
    |目标库信息|实例类型|选择**RDS实例**。|
    |实例地区|选择目标RDS实例所属地域。|
    |RDS实例ID|选择目标RDS实例ID。|
    |数据库名称|填入RDS实例中待迁入数据的数据库名，可以和自建PostgreSQL中待迁移的数据库名不同。 **说明：** 您需要先在RDS实例中创建相应的数据库和schema，详情请参见[准备工作二：在目标实例中创建数据库和schema](#section_c5a_y2m_0fs)。 |
    |数据库账号|填入目标RDS的数据库账号，权限要求请参见[数据库账号的权限要求](#section_577_f3j_wg1)。|
    |数据库密码|填入该数据库账号对应的密码。 **说明：** 目标库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的目标库信息是否正确。目标库信息填写正确则提示**测试通过**；如果提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的目标库信息。 |

6.  配置完成后，单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到目标RDS实例的白名单中，用于保障DTS服务器能够正常连接目标RDS实例。

7.  选择迁移对象及迁移类型。

    ![选择迁移类型和迁移对象](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5397549951/p47966.png)

    |配置|说明|
    |:-|:-|
    |迁移类型|    -   如果只需要进行全量数据迁移，则同时选中**结构迁移**和**全量数据迁移**。
    -   如果需要进行不停机迁移，则同时选中**结构迁移**、**全量数据迁移**和**增量数据迁移**。本案例中同时选中这三种迁移类型。
**说明：** 如果未选中**增量数据迁移**，为保障数据一致性，全量数据迁移期间请勿在自建PostgreSQL数据库中写入新的数据。 |
    |迁移对象|在迁移对象框中单击待迁移的对象，然后单击![向右小箭头](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8502659951/p40698.png)图标将其移动至已选择对象框。

**说明：**

    -   迁移对象选择的粒度为Schema、表、列。
    -   默认情况下，迁移完成后，迁移对象名跟自建PostgreSQL数据库一致。如果您需要迁移对象在目标RDS实例上名称不同，那么需要使用DTS提供的对象名映射功能。使用方法请参见[库表列映射](/intl.zh-CN/数据迁移/迁移任务管理/库表列映射.md)。
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

12. 将业务切换至RDS实例。

## 后续操作

用于数据迁移的数据库账号拥有读写权限，为保障数据库安全性，请在数据迁移完成后，删除自建PostgreSQL数据库和RDS PostgreSQL实例中的数据库账号。

