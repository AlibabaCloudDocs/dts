# Migrate data from an ApsaraDB RDS for PPAS instance to a PolarDB-O cluster

This topic describes how to migrate data from an ApsaraDB RDS for PPAS instance to a PolarDB-O cluster by using Data Transmission Service \(DTS\). DTS supports schema migration, full data migration, and incremental data migration. When you configure a data migration task, you can select all of the supported migration types to ensure service continuity.

## Prerequisites

-   A PolarDB-O cluster is created. For more information, see [Create a PolarDB-O cluster]().
-   The available storage space of the PolarDB-O cluster is larger than the total size of the data in the ApsaraDB RDS for PPAS instance.
-   To migrate incremental data from an ApsaraDB RDS for PPAS instance, you must [submit a ticket](https://workorder-intl.console.aliyun.com/?#/ticket/add/?productId=1226) to grant the superuser permission to the database account.

## Precautions

-   DTS uses read and write resources of the source and destination databases during full data migration. This may increase the loads of the database servers. If the database performance is unfavorable, the specification is low, or the data volume is large, database services may become unavailable. For example, DTS occupies a large amount of read and write resources in the following cases: a large number of slow SQL queries are performed on the source database, the tables have no primary keys, or a deadlock occurs in the destination database. Before you migrate data, evaluate the impact of data migration on the performance of the source and destination databases. We recommend that you migrate data during off-peak hours. For example, you can migrate data when the CPU utilization of the source and destination databases is less than 30%.
-   The tables to be migrated in the source database must have PRIMARY KEY or UNIQUE constraints and all fields must be unique. Otherwise, the destination database may contain duplicate data records.
-   A single data migration task can migrate data from only one database. To migrate data from multiple databases, you must create a data migration task for each database.
-   If a data migration task fails, DTS automatically resumes the task. Before you switch your workloads to the destination cluster, stop or release the data migration task. Otherwise, the data in the source database will overwrite the data in the destination cluster after the task is resumed.
-   After your workloads are switched to the destination database, newly written sequences do not increment from the maximum value of the sequences in the source database. Therefore, you must query the maximum value of the sequences in the source database before you switch your workloads to the destination database. Then, you must specify the queried maximum value as the starting value of the sequences in the destination database. You can run the following statements to query the maximum value of the sequences in the source database:

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


## Migration types

|Migration type|Description|
|--------------|-----------|
|Schema migration|DTS migrates the schemas of the required objects from the source database to the destination PolarDB-O cluster. DTS supports schema migration for the following types of objects: table, view, synonym, trigger, stored procedure, function, package, and user-defined type. **Note:** However, if an object contains triggers, data will become inconsistent between the source and destination databases. |
|Full data migration|DTS migrates historical data of the required objects from the source database to the destination PolarDB-O cluster. **Note:** During schema migration and full data migration, we recommend that you do not perform data definition language \(DDL\) operations on the required objects. Otherwise, the objects may fail to be migrated. |
|Incremental data migration|DTS retrieves redo log files from the source database. Then, DTS synchronizes incremental data from the source database to the destination PolarDB-O cluster. DTS can synchronize data manipulation language \(DML\) operations, including INSERT, UPDATE, and DELETE operations. DTS cannot synchronize DDL operations.Incremental data migration allows you to ensure service continuity when you migrate data from an ApsaraDB RDS for PPAS instance to a PolarDB-O cluster. |

## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|--------------|--------------------------|--------------------|
|Schema migration and full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|
|Incremental data migration|Charged. For more information, see [Pricing]().|

## Permissions required for database accounts

Log on to the source Oracle database, create an account for data collection, and grant permissions to the account.

|Database|Schema migration|Full data migration|Incremental data migration|
|--------|----------------|-------------------|--------------------------|
|ApsaraDB RDS for PPAS|The read permission|The read permission|The superuser permission|
|PolarDB-O|The owner permission on schemas|The owner permission on schemas|The owner permission on schemas|

For more information about how to create and authorize a database account, see the following topics:

-   ApsaraDB RDS for PPAS instance: [Create an account on an ApsaraDB RDS for PPAS instance](/intl.en-US/RDS PPAS Database/Account/Create an account on an ApsaraDB RDS for PPAS instance.md)

    **Note:** To migrate incremental data from an ApsaraDB RDS for PPAS instance, you must [submit a ticket](https://workorder-intl.console.aliyun.com/?#/ticket/add/?productId=1226) to grant the superuser permission to the database account.

-   PolarDB-O cluster: [Create database accounts](~~118194~~)

## Procedure

1.  Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

2.  In the left-side navigation pane, click **Data Migration**.

3.  At the top of the Migration Tasks page, select the region where the destination cluster resides.

    ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4.  In the upper-right corner of the page, click **Create Migration Task**.

5.  Configure the source and destination databases.

    ![Configure the source and destination databases](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1863158951/p68785.png)

    |Section|Parameter|Description|
    |:------|:--------|:----------|
    |N/A|Task Name|DTS automatically generates a task name. We recommend that you specify an informative name for easy identification. You do not need to use a unique task name.|
    |Source Database|Instance Type|Select **User-Created Database with Public IP Address**. You cannot select ApsaraDB RDS for PPAS as the instance type.|
    |Instance Region|If the instance type is set to **User-Created Database with Public IP Address**, you do not need to specify the **instance region**. **Note:** You can click **Get IP Address Segment of DTS** to obtain the CIDR blocks of DTS servers. Then, you can add the CIDR blocks of DTS servers to the whitelist of the ApsaraDB RDS for PPAS instance. For more information, see [Configure an IP address whitelist for an ApsaraDB RDS for PPAS instance](/intl.en-US/RDS PPAS Database/Quick start/Configure a whitelist for an ApsaraDB RDS for PPAS instance.md). |
    |Database Type|Select **PPAS**.|
    |Version|Select the database engine version of the ApsaraDB RDS for PPAS instance.|
    |Hostname or IP Address|Enter the public endpoint of the ApsaraDB RDS for PPAS instance.|
    |Port Number|Enter the service port number of the ApsaraDB RDS for PPAS instance. The default port number is **3433**.|
    |Database Name|Enter the name of the source database in the ApsaraDB RDS for PPAS instance.|
    |Database Account|Enter the database account of the ApsaraDB RDS for PPAS instance. For more information about the permission requirements, see [Permissions required for database accounts](#section_tjy_iim_mgi).|
    |Database Password|Enter the password of the source database account. **Note:** After you specify the source database parameters, click **Test Connectivity** next to **Database Password** to verify whether the specified parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the source database parameters based on the check results. |
    |Destination Database|Instance Type|Select **PolarDB**.|
    |Instance Region|Select the region where the destination PolarDB-O cluster resides.|
    |PolarDB Instance ID|Select the ID of the destination PolarDB-O cluster.|
    |Database Name|Enter the name of the destination database.|
    |Database Account|Enter the database account of the destination PolarDB-O cluster. For more information about the permission requirements, see [Permissions required for database accounts](#section_tjy_iim_mgi).|
    |Database Password|Enter the password of the destination database account. **Note:** After you specify the destination database parameters, click **Test Connectivity** next to **Database Password** to verify whether the parameters are valid. If the specified parameters are valid, the **Passed** message appears. If the **Failed** message appears, click **Check** next to **Failed**. Modify the destination database parameters based on the check results. |

6.  In the lower-right corner of the page, click **Set Whitelist and Next**.

    **Note:** DTS adds the CIDR blocks of DTS servers to the whitelists of the destination PolarDB-O cluster. This ensures that DTS servers can connect to the destination PolarDB-O cluster.

7.  Select the migration types and the objects to be migrated.

    ![Select the migration types and the objects to be migrated](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8134948951/p62979.png)

    |Setting|Description|
    |:------|:----------|
    |Select the migration types|    -   To perform only full data migration, select **Schema Migration** and **Full Data Migration**.
    -   To ensure service continuity during data migration, select **Schema Migration**, **Full Data Migration**, and **Incremental Data Migration**.
**Note:**

    -   If **Incremental Data Migration** is not selected, we recommend that you do not write data to the source database during full data migration. This ensures data consistency between the source and destination databases.
    -   During schema migration and full data migration, we recommend that you do not perform DDL operations on the required objects. Otherwise, the objects may fail to be migrated. |
    |Select the objects to be migrated|Select one or more objects from the Available section and click the ![Right arrow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p40698.png) icon to move the objects to the Selected section.

**Note:**

    -   You can select a database as the object to be migrated. You can also select columns or tables as the objects to be migrated.
    -   By default, after an object is migrated to the destination database, the name of the object remains unchanged. You can use the object name mapping feature to change the names of the objects that are migrated to the destination database. For more information, see [Object name mapping](/intl.en-US/Data Migration/Migration task management/Object name mapping.md).
    -   If you use the object name mapping feature on an object, other objects that are dependent on the object may fail to be migrated. |
    |Specify the retry time for failed connection to the source or destination database|By default, if DTS fails to connect to the source or destination database, DTS retries within the next 12 hours. You can specify the retry time based on your needs. If DTS reconnects to the source and destination databases within the specified time, DTS resumes the data migration task. Otherwise, the data migration task fails.**Note:** When DTS retries a connection, you are charged for the DTS instance. We recommend that you specify the retry time based on your business needs. You can also release the DTS instance at your earliest opportunity after the source and destination instances are released. |

8.  In the lower-right corner of the page, click **Precheck**.

    **Note:**

    -   Before you can start the data migration task, a precheck is performed. DTS can migrate data only if the precheck is past.
    -   If the precheck fails, click the ![Prompt](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) icon next to each failed item to view details. Fix the issues as prompted and run the precheck again.
9.  After the precheck is past, click **Next**.

10. In the Confirm Settings dialog box, specify the **Channel Specification** parameter and select **Data Transmission Service \(Pay-As-You-Go\) Service Terms**.

11. Click **Buy and Start** to start the data migration task.

    -   Schema migration and full data migration

        Do not manually stop the migration task. Otherwise, the data may be incomplete. You need only to wait until the migration task is complete. The migration task can automatically stop.

    -   Schema migration, full data migration, and incremental data migration

        The migration task cannot automatically stop. You must manually stop the migration task.

        **Note:** Manually stop the migration task at an appropriate time. For example, you can stop the migration task during off-peak hours of your business or before you switch your business to the destination cluster.

        1.  Wait until **Incremental Data Migration** and **The migration task is not delayed** appear in the progress bar of the migration task. Then, stop writing data to the source database for a few minutes. The delay time may appear in the status for **incremental migration**.
        2.  Wait until the status of **Incremental Data Migration** for the migration task changes to **The migration task is not delayed** again. Then, manually stop the migration task.

            ![Stop an incremental migration task](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2940359951/p47604.png)


