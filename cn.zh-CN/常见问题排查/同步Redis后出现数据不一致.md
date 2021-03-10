# 同步Redis后出现数据不一致

本文介绍判断或排查同步Redis出现数据不一致的方法。

## 问题描述

使用数据传输服务（DTS）同步Redis后，出现了源端和目标端数据不一致的现象。

## 问题示例

使用数据传输服务（DTS）同步完Redis后，使用Redis的`info keyspace`命令查看源端和目标端的keys情况，出现源端和目标端数据不一致，且含过期时间的keys占很大比重。如下图：

![含过期时间的key占很大比重](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4794774161/p245368.png)

## 可能原因

单靠`info keyspace`的`expires`来判断数据是否一致会出现偏差。其统计信息的统计值不是精确值，有一定的误差。

-   `info keyspace`的输出结果中：
    -   `keys`表示所有keys的数据量。
    -   `expires`表示带有过期keys的数据量，包含未过期和已过期的keys的数量。

因此，通过`expires`来判断数据量时，逻辑上已经过期但未被Redis删除的keys也会被统计进来，从而出现了源端和目标端的数据不一致的现象。

**说明：** Redis处理过期keys的时机主要为以下几种：

-   CPU空闲时清理过期keys。
-   实际访问keys时会判断keys是否过期，并逐出过期keys。
-   Scan命令扫描时判断keys是否过期，并逐出过期keys。

关于Redis的详细清理策略，参考：[Redis过期Key的处理](https://developer.aliyun.com/article/257459)。

## 解决方法

建议可通过以下2种方法判断数据是否一致：

**说明：** 同步Redis数据的任务，若想看同步之后数据是否有丢失，需要看源数据keys是否已经过期。已过期的数据也会被统计到`expires`中，因此，不能单靠`expires`去判断数据的一致性。

1.  对比A（源库keys数量减去expires后得到的数量）与B（目标库keys数量减去expires后得到的数量），若A和B的差值很小，则可判断数据一致。
2.  用redis-full-check校验。具体方法可参考：[redis-full-check工具的使用](https://developer.aliyun.com/article/690463)。

**说明：** 如何让Redis `info keyspace`统计的内存使用量、数据量看起来一致？

建议清除过期的Redis数据，云数据库和自建数据库的方法如下：

-   云数据库Redis：登录[Redis控制台](https://kvstorenext.console.aliyun.com/?spm=5176.54432.J_5834642020.4.62d249f0zSgsLO#/home/cn-hangzhou)，执行清除数据操作。

    ![清除数据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4794774161/p245374.png)

-   自建Redis数据库：使用redis-full-check做校验，检验的过程中执行SCAN 等命令触发过期数据清理动作。

