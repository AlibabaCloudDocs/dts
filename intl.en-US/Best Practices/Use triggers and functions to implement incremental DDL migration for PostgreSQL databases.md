# Use triggers and functions to implement incremental DDL migration for PostgreSQL databases

Before you use Data Transmission Service \(DTS\) to migrate data between PostgreSQL databases, you can create a function and a trigger in the source database. The function and trigger obtain the data definition language \(DDL\) information of the source database. During incremental data migration, DTS migrates DDL operations to the destination database.

-   The source database is a user-created PostgreSQL database V9.4 or later, or an ApsaraDB RDS for PostgreSQL instance V12, V11, or V10. If the source database is an ApsaraDB RDS for PostgreSQL instance V10, the storage type of the instance must be Enhanced SSD or Standard SSD.

    **Note:** The source database cannot be an ApsaraDB RDS for PostgreSQL instance V10 or V9.4 whose storage type is Local SSD.

-   A data migration task was created after October 1, 2020.

When you use DTS to migrate data between PostgreSQL databases, DTS synchronizes only data manipulation language \(DML\) operations during incremental data migration. DML operations include INSERT, DELETE, and UPDATE. DTS does not synchronize DDL operations during incremental data migration.

To synchronize DDL operations, you can create a trigger and a function to obtain the DDL information of the source database. During incremental data migration, DTS migrates DDL operations to the destination database.

**Note:** Only the following DDL operations can be synchronized: CREATE TABLE, DROP TABLE, and ALTER TABLE. The ALTER TABLE operation includes RENAME TABLE, ADD COLUMN, and DROP COLUMN.

## Procedure

**Warning:** If you need to migrate incremental data from multiple databases, repeat Steps 2 to 5 for each database.

1.  Log on to the source PostgreSQL database. For more information, see [Connect to an ApsaraDB RDS for PostgreSQL instance](/intl.en-US/RDS PostgreSQL Database/Quick start/Connect to an ApsaraDB RDS for PostgreSQL instance.md) or [psql](https://www.postgresql.org/docs/current/app-psql.html).

2.  Switch to the source database.

    **Note:** The psql tool is used in this example. You can run the `\c <Database name>` command to switch to the source database, for example, `\c dtststdata`.

3.  Run the following command to create a table that stores the DDL information:

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

4.  Run the following command to create a function that obtains the DDL information:

    ```
    CREATE FUNCTION public.dts_capture_ddl()
        RETURNS event_trigger
        LANGUAGE 'plpgsql'
        COST 100
        VOLATILE NOT LEAKPROOF SECURITY DEFINER
    AS $BODY$
      declare ddl_text text;
      declare max_rows int := 10000;
      declare current_rows int;
      declare pg_version_95 int := 90500;
      declare pg_version_10 int := 100000;
      declare current_version int;
      declare object_id varchar;
      declare alter_table varchar;
      declare record_object record;
      declare message text;
      declare pub RECORD;
    begin
    
      select current_query() into ddl_text;
    
      if TG_TAG = 'CREATE TABLE' then -- ALTER TABLE schema.TABLE REPLICA IDENTITY FULL;
        show server_version_num into current_version;
        if current_version >= pg_version_95 then
          for record_object in (select * from pg_event_trigger_ddl_commands()) loop
            if record_object.command_tag = 'CREATE TABLE' then
              object_id := record_object.object_identity;
            end if;
          end loop;
        else
          select btrim(substring(ddl_text from '[ \t\r\n\v\f]*[c|C][r|R][e|E][a|A][t|T][e|E][ \t\r\n\v\f]*.*[ \t\r\n\v\f]*[t|T][a|A][b|B][l|L][e|E][ \t\r\n\v\f]+(. *)\(.*'),' \t\r\n\v\f') into object_id;
        end if;
        if object_id = '' or object_id is null then
          message := 'CREATE TABLE, but ddl_text=' || ddl_text || ', current_query=' || current_query();
        else
          alter_table := 'ALTER TABLE ' || object_id || ' REPLICA IDENTITY FULL';
          message := 'alter_sql=' || alter_table;
          execute alter_table;
        end if;
        if current_version >= pg_version_10 then
          for pub in (select * from pg_publication where pubname like 'dts_sync_%') loop
            raise notice 'pubname=%',pub.pubname;
            BEGIN
              execute 'alter publication ' || pub.pubname || ' add table ' || object_id;
            EXCEPTION WHEN OTHERS THEN
            END;
          end loop;
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

5.  Change the owner of the function to the account that is used to connect to the source database, for example, postgresql.

    ```
    ALTER FUNCTION public.dts_capture_ddl()
        OWNER TO postgres;
    ```

6.  Run the following command to create a global event trigger:

    ```
    CREATE EVENT TRIGGER dts_intercept_ddl ON ddl_command_end
    EXECUTE PROCEDURE public.dts_capture_ddl();
    ```


Configure a data migration task. For more information, see the following topics:

-   [Migrate incremental data from a user-created PostgreSQL database \(versions 9.4 to 10.0\) to an ApsaraDB RDS for PostgreSQL instance](/intl.en-US/Data Migration/Migration from a user-created database to Alibaba Cloud/Source database: PostgreSQL/Migrate incremental data from a user-created PostgreSQL database (versions 9.4 to
         10.0) to an ApsaraDB RDS for PostgreSQL instance.md)
-   [Migrate incremental data from a user-created PostgreSQL database \(version 10.1 to 12\) to an ApsaraDB RDS for PostgreSQL instance](/intl.en-US/Data Migration/Migration from a user-created database to Alibaba Cloud/Source database: PostgreSQL/Migrate incremental data from a user-created PostgreSQL database (version 10.1 to
         12) to an ApsaraDB RDS for PostgreSQL instance.md)

**Note:** After the data migration task is released, you must log on to the source PostgreSQL database and run the following command to delete the trigger and function.

```
drop EVENT trigger dts_intercept_ddl;
drop function public.dts_capture_ddl();
drop table public.dts_ddl_command;
```

