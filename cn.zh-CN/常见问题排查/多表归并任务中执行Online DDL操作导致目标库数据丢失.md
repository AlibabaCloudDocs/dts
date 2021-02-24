# 多表归并任务中执行Online DDL操作导致目标库数据丢失

通过DTS进行多表归并任务时，若在源库执行Online DDL操作，则会导致目标库数据丢失。本文介绍如何避免这种情况的发生。

## 问题描述

在通过DTS进行多表归并任务时，若在源库进行Online DDL操作，对源库的表结构等进行了调整修改，则同步到目标库时，会出现数据丢失的情况。

**说明：** 多表归并任务指将不同源库的表进行归并，继而同步或迁移到目标库。

例如：当用户配置了从源库1 RDS MySQL和源库2 RDS MySQL到目标库RDS MySQL的多表归并任务，则数据迁移的逻辑如下：

-   源库1的表A1迁移至目标库表A。
-   源库2的表A2迁移至目标库表A。

假设源库1的表A1中100条记录，源库2的表A2中200条记录，则多表归并后，目标库表A中应有300条记录。

## 问题示例

以RDS MySQL（源库）向RDS MySQL（目标库）进行多表归并任务为例，说明源库进行Online DDL操作导致目标库数据缺失的情况。

多表归并场景：用户配置了从源库1 RDS MySQL和源库2 RDS MySQL到目标库RDS MySQL的多表归并任务，数据迁移逻辑如下：

-   源库1的表A1迁移至目标库表A。
-   源库2的表A2迁移至目标库表A。

假设源库1的表A1中100条记录，源库2的表A2中200条记录，则多表归并后，目标库表A中应有300条记录。

在源库1的表A1中进行了如下Online DDL操作：

**说明：** Online DDL操作，参见[无锁结构变更](https://help.aliyun.com/document_detail/98373.html?spm=5176.11065259.1996646101.searchclickresult.4a8d1ad6znD2QI)。

-   创建临时表：

    ```
    CREATE TABLE tmp_table_table LIKE table_name
    ```

-   变更临时表结构：

    ```
     ALTER TABLE tmp_table_table XXXX
    ```

-   全量拷贝数据：

    ```
    INSERT IGNORE INTO tmp_table_table (SELECT %s FROM table_name FORCE INDEX (%s) WHERE xxx
    ```

-   增量数据Binlog同步：

    ```
    UPDATA/INSRT/DELETE tmp_table_name
    ```

-   切换新旧表：

    ```
    RENAME TABLE table_name to old_tmp_table_table, tmp_table_name to table_name
    ```


在上面的内部处理中，会创建临时表、变更临时表结构、并将原始表的全量数据拷贝到临时表，最后会使用 rename 命令将临时表切换成新的业务表。这样的处理同步到目的端的结果，会导致源库1的表A1的数据替换了目标库表A的数据，源库2的表A2的数据就丢失了。

## 可能原因

Online DDL操作的内部机制导致了源端的数据会覆盖目标端的数据。

## 解决方案

-   任务设置时，设置为不同步Online DDL操作。
-   禁止在源端执行Online DDL操作。

