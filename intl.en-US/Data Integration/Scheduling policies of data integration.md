# Scheduling policies of data integration

Data Transmission Service \(DTS\) provides the data integration feature. You can configure a scheduling policy to migrate schemas and historical data from the source database to the destination database on a regular basis. This feature allows you to flexibly build data warehouses. For example, you can build a T+1 data warehouse. This topic describes the scheduling policies of data integration, the limits on scheduling policies, and the scenarios of these policies.

## Supported databases

All databases that support full data migration support the data integration feature. For more information, see [Supported databases and migration types](/intl.en-US/Data Migration/Supported databases and migration types.md).

## Billing

|Migration type|Instance configuration fee|Internet traffic fee|
|--------------|--------------------------|--------------------|
|Schema migration and full data migration|Free of charge|Charged only when data is migrated from Alibaba Cloud over the Internet. For more information, see [Pricing]().|

## Configuration method

You can configure a scheduling policy for a regular data migration task in the **Configure Migration Types and Objects** step.

**Note:** For more information about how to configure a scheduling policy, see [Configure a data integration task between ApsaraDB RDS for MySQL instances](/intl.en-US/Data Integration/Configure a data integration task between ApsaraDB RDS for MySQL instances.md).

## Scheduling policies

|Scheduling policy|Description|Scenario|
|-----------------|-----------|--------|
|**Multi-replica mode**|DTS migrates schemas and historical data of the required objects from the source database to the destination database based on the scheduling cycle. **Note:**

-   During task scheduling, DTS creates a database in the destination instance to receive migrated data. The database name is suffixed with `_yyyymmdd_HH_mm_ss`, which indicates the scheduled time. For example, a data migration task migrates data from a database named dtstestdata. The task is scheduled at 11:00:00 on February 10, 2020. When DTS migrates data at this time, it creates a database named `dtstestdata_20200210_11_00_00` in the destination instance.
-   The destination database must have sufficient space to store the data that is migrated during each scheduling cycle.

|This policy is applicable to test scenarios where test operations are performed on multiple replicas of historical data.|
|**Resynchronization mode**|At the first scheduled time, DTS migrates schemas and historical data of the required objects to the destination database. At each subsequent scheduled time, DTS migrates schemas and historical data of the required objects to the destination database again. **Warning:**

-   Before each scheduled time, you must manually delete the schemas and data that have been migrated to the destination database at the last scheduled time. Otherwise, the data migration fails.
-   If DTS does not support schema migration from a specific database, you must create a schema in the destination database based on the schema of the required objects.

|This policy is applicable to scenarios where only the latest historical data is maintained in the destination database for testing or data analysis.|
|**Timestamp-based incremental data mode**|DTS migrates incremental data that is generated after the previous scheduling to the destination database based on the specified timestamp. This policy has the following limits: -   This policy is supported only when the source and destination databases are MySQL databases.
-   Each source table must contain a timestamp field. You must specify the timestamp field of each table when you configure a data migration task.
-   Source tables cannot have FOREIGN KEY constraints. Otherwise, data migration may fail. If you update a child table that has FOREIGN KEY constraints in the destination database and then update the attached table, an error occurs when data is inserted into the child table.
-   Data that is deleted by using the DELETE or TRUNCATE command cannot be migrated.

**Note:**

-   If a table does not have a primary key, DTS uses an entire data record as the primary key. This means that a data record is matched only if all fields in the data record are matched. DTS deletes matched data records and inserts new data records.
-   If two primary keys conflict with each other when a data record is written to the destination database, DTS runs the REPLACE INTO command to insert the data record.

|This policy is applicable to lightweight data migration scenarios where you do not need to migrate all historical data each time. For example, you can use this policy when you build a T+1 data warehouse.|

