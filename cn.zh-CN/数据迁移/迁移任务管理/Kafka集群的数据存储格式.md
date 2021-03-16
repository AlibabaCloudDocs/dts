# Kafka集群的数据存储格式

DTS支持选择迁移或同步到Kafka集群的数据存储格式，本文为您介绍数据格式的定义说明，方便您根据定义解析数据。

## 数据存储格式

DTS支持将写入至Kafka集群的数据存储为如下三种格式：

-   [DTS Avro](#section_gxp_eqj_cv9)：一种数据序列化格式，可以将数据结构或对象转化成便于存储或传输的格式。
-   [Shareplex Json](#section_a46_8d4_tvk)：数据复制软件[Shareplex](https://www.quest.com/cn-zh/products/shareplex/)读取源库中的数据，将数据写入至Kafka集群时，数据存储格式为Shareplex Json。
-   [Canal Json](#section_dt3_dut_0t1)：[Canal](https://github.com/alibaba/canal/wiki)解析数据库增量日志，并将增量数据传输至Kafka集群，数据存储格式为Canal Json。

## DTS Avro

**DTS Avro**为默认存储格式。您需要根据DTS Avro的schema定义进行数据解析，schema定义详情请参见迁移或同步至Kafka集群中的数据均[DTSAvro的schema定义](https://github.com/LioRoger/subscribe_example/tree/master/avro)。

## Shareplex Json

|参数|说明|
|--|--|
|`time`|数据库中事务的提交时间，格式为yyyy-MM-ddTHH:mm:ssZ（UTC时间）。|
|`userid`|提交事务的用户ID。|
|`op`|数据操作类型，包括INSERT, UPDATE, DELETE, TRUNCATE, DROP COLUMN, UPDATE BEFORE, UPDATE AFTER。|
|`scn`|系统变化编号SCN（System Change Number），用以标识数据库在某个确切时刻提交事务的版本。每个已提交的事务分配一个唯一的SCN。|
|`rowid`|用于定位数据库中一条记录的一个相对唯一地址值。|
|`trans`|事务ID。|
|`seq`|事务内部的操作序号，从1开始记录。|
|`size`|事务内部的操作总数。|
|`table`|表名。|
|`idx`|事务内部操作的索引，格式为`seq/size`，例如`1/11`表示，在操作总数为11的事务内部，该操作的序号为1。|
|`posttime`|事务提交至目标库的时间。|

示例如下：

-   插入数据：

    ```
    {
        "meta": {
            "time": "2017-06-16T14:24:34", 
            "userid": 84,                                    
            "op": "ins",                                   
              "scn": "14589063118712",                  
              "rowid": "AAATGpAAIAAItcIAAA",      
            "trans": "7.0.411499",                 
            "seq": 1,                                          
            "size": 11,                                         
            "table": "CL_BIZ1.MIO_LOG",       
              "idx": "1/11",                                       
            "posttime": "2017-06-16T14:33:52"
        },
        "data": {
            "MIO_LOG_ID": "32539737"
         }
    }
    ```

-   更新数据：

    ```
    {
        "meta": {
            "time": "2017-06-16T15:38:13",
            "userid": 84,
            "op": "upd",                             
            "table": "CL_BIZ1.MIO_LOG"
            ….
        },
        "data": {                                          
            "CNTR_NO": "1171201606"
        },
        "key": {                                            
            "MIO_LOG_ID": "32537893",
            "PLNMIO_REC_ID": "31557806",
            "POL_CODE": null,
            "CNTR_TYPE": null,
            "CNTR_NO": "1171201606syui26"
        }
    }
    ```

-   删除数据：

    ```
    {
        "meta": {
            "time": "2017-06-16T15:51:35",
            "userid": 84,
            "op": "del",                      
         },
        "data": {                                    
            "MIO_LOG_ID": "32539739",
            "PLNMIO_REC_ID": "31557806",
            "POL_CODE": null,
            "CNTR_TYPE": null,
            "CG_NO": null
         }
    }
    ```


## Canal Json

|参数|说明|
|--|--|
|`database`|数据库名称。|
|`es`|操作在源库的执行时间，13位Unix时间戳，单位为毫秒。**说明：** Unix时间戳转换工具可用搜索引擎获取。 |
|`id`|操作的序列号。|
|`isDdl`|是否是DDL操作。-   true：是。
-   false：否。 |
|`mysqlType`|字段的数据类型。|
|`old`|变更前的数据。|
|`pkNames`|主键名称。|
|`sql`|SQL语句。|
|`sqlType`|经转换处理后的字段类型，如unsigned int会被转化为Long，unsigned long会被转换为BigDecimal。|
|`table`|表名。|
|`ts`|操作写入到目标库的时间，13位Unix时间戳，单位为毫秒。**说明：** Unix时间戳转换工具可用搜索引擎获取。 |
|`type`|操作的类型，比如DELETE、UPDATE、INSERT。|

更新数据的示例如下：

```
  "data": [
    {
      "id": "500000287",
      "shipping_type": null
    }
  ],
  "database": "dbname",
  "es": 1600161894000,
  "id": 58,
  "isDdl": false,
  "mysqlType": {
    "id": "bigint(20)",
    "shipping_type": "varchar(50)"
  },
  "old": [
    {
      "shipping_type": "aaa"
    }
  ],
  "pkNames": [
    "id"
  ],
  "sql": "",
  "sqlType": {
    "id": -5,
    "shipping_type": 12
  },
  "table": "tablename",
  "ts": 1600161894771,
  "type": "UPDATE"
}
```

