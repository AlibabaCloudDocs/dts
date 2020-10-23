# 通过触发器和函数实现PostgreSQL的DDL增量迁移

在使用DTS执行PostgreSQL数据库间的数据迁移前，可通过本文介绍的方法在源库创建触发器和函数获取源库的DDL信息，然后再由DTS执行数据迁移，在增量数据迁移阶段即可实现DDL操作的增量迁移。

-   源库需为9.4及以上版本的自建PostgreSQL数据库。
-   数据迁移任务需在2020年10月1日之后创建。

通过DTS执行PostgreSQL数据库间的数据迁移时，在增量数据迁移阶段，DTS仅支持DML操作（INSERT、DELETE、UPDATE）的同步，不支持DDL操作的同步。

通过本文的方法先在源库中创建触发器和函数来捕获DDL信息，再由DTS执行数据迁移，即可实现DDL操作的同步。

**说明：** 仅支持表级别DDL操作的同步：CREATE TABLE、DROP TABLE、ALTER TABLE（包含RENAME TABLE、ADD COLUMN、DROP COLUMN）。

## 操作步骤

**警告：** 如果源库中有多个数据库需要执行增量数据迁移，您需要重复执行步骤2到步骤5。

1.  登录源PostgreSQL数据库，相关方法请参见[连接PostgreSQL实例](/intl.zh-CN/RDS PostgreSQL 数据库/快速入门/连接PostgreSQL实例.md)或[psql工具介绍](https://www.postgresql.org/docs/current/app-psql.html)。

2.  切换至待迁移的数据库。

    **说明：** 本案例以psql工具为例介绍，您可以使用`\c <数据库名>`命令来切换数据库，例如`\c dtststdata`。

3.  执行下述命令创建存放DDL信息的表。

    ```
    CREATE TABLE public.dts_ddl_command
    (
        ddl_text text COLLATE pg_catalog."default",
       id bigserial primary key,
       event text COLLATE pg_catalog."default",
       tag text COLLATE pg_catalog."default",
       username character varying COLLATE pg_catalog."default",
       database character varying COLLATE pg_catalog."default",
       schema character varying COLLATE pg_catalog."default",
       object_type character varying COLLATE pg_catalog."default",
       object_name character varying COLLATE pg_catalog."default",
       client_address character varying COLLATE pg_catalog."default",
       client_port integer,
       event_time timestamp with time zone,
       txid_current character varying(128) COLLATE pg_catalog."default",
       message text COLLATE pg_catalog."default"
    );
    ```

4.  执行下述命令创建捕获DDL信息的函数。

    ```
    CREATE FUNCTION public.dts_capture_ddl()
        RETURNS event_trigger
        LANGUAGE 'plpgsql'
        COST 100
        VOLATILE NOT LEAKPROOF SECURITY DEFINER
    AS $BODY$
      declare ddl_text text;
      declare max_rows int := 100000;
      declare current_rows int;
      declare pg_version_95 int := 90500;
      declare current_version int;
      declare object_id varchar;
      declare alter_table varchar;
      declare record_object record;
      declare message text;
    begin
    
      select current_query() into ddl_text;
    
      if TG_TAG = 'CREATE TABLE' then -- ALTER TABLE schema.TABLE REPLICA IDENTITY FULL;
        show server_version_num into current_version;
        if current_version >= pg_version_95 then
          select * into record_object from pg_event_trigger_ddl_commands();
          object_id := record_object.object_identity;
        else
          select btrim(substring(ddl_text from '[ \t\r\n\v\f]*[c|C][r|R][e|E][a|A][t|T][e|E][ \t\r\n\v\f]*.*[ \t\r\n\v\f]*[t|T][a|A][b|B][l|L][e|E][ \t\r\n\v\f]+(.*)\(.*'),' \t\r\n\v\f') into object_id;
        end if;
        object_id := btrim(object_id,' \t\r\n\v\f');
        if object_id = '' or object_id is null then
          message := 'CREATE TABLE, but ddl_text=' || ddl_text || ', current_query=' || current_query();
        else
          alter_table := 'ALTER TABLE ' || object_id || ' REPLICA IDENTITY FULL';
          message := 'alter_sql=' || alter_table;
          execute alter_table;
        end if;
      end if;
    
      insert into public.dts_ddl_command(id,event,tag,username,database,schema,object_type,object_name,client_address,client_port,event_time,ddl_text,txid_current,message)
      values (default,TG_EVENT,TG_TAG,current_user,current_database(),current_schema,'','',inet_client_addr(),inet_client_port(),current_timestamp,ddl_text,cast(TXID_CURRENT() as varchar(16)),message);
    
      select count(id) into current_rows from public.dts_ddl_command;
      if current_rows > max_rows then
        delete from public.dts_ddl_command where id in (select min(id) from public.dts_ddl_command);
      end if;
    end
    $BODY$;
    ```

5.  将刚创建的函数的所有者修改为postgres。

    ```
    ALTER FUNCTION public.dts_capture_ddl()
        OWNER TO postgres;
    ```

6.  执行下述命令创建全局事件触发器。

    ```
    CREATE EVENT TRIGGER dts_intercept_ddl ON ddl_command_end
    EXECUTE PROCEDURE public.dts_capture_ddl();
    ```


根据源库的版本，选择下述步骤配置数据迁移任务：

-   [从自建PostgreSQL（9.4-10.0版本）增量迁移至RDS PostgreSQL](/intl.zh-CN/数据迁移/从自建数据库迁移至阿里云/源库为PostgreSQL/从自建PostgreSQL（9.4-10.0版本）增量迁移至RDS PostgreSQL.md)
-   [从自建PostgreSQL（10.1-12版本）增量迁移至RDS PostgreSQL](/intl.zh-CN/数据迁移/从自建数据库迁移至阿里云/源库为PostgreSQL/从自建PostgreSQL（10.1-12版本）增量迁移至RDS PostgreSQL.md)

**说明：** 数据迁移任务释放后，您需要登录源PostgreSQL数据库，执行下述命令删除触发器和函数。

```
DROP TRIGGER dts_intercept_ddl;
drop function public.dts_capture_ddl;
drop table public.dts_ddl_command;
```

