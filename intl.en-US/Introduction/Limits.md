# Limits

This topic describes the limits of Data Transmission Service \(DTS\).

## Data migration mode

The following sections list the limits relating to the data migration mode of DTS.

|Limits|Negative effect of failing to follow the limits|
|:-----|:----------------------------------------------|
|The following data types are not supported: sql\_variant, hierarchyid, geometry, cursor, and rowversion.|-   Changes to values of the image, text, ntext, and xml data types are not written into the log. DTS can only ensure the values of these data types are consistent between the source and target databases.
-   If unsupported data types are used in the source database, this part of the data is not migrated. |
|Heap tables are not supported.|These tables cannot be migrated..|
|Tables that contain non-clustered indexes are not supported.|These tables cannot be migrated.|

|Limits|Negative effect of failing to follow the limits|
|:-----|:----------------------------------------------|
|The following data types are not supported: URI, UDT, BFile, and UROWID.|Data fails to be migrated.|

|Limits|Negative effect of failing to follow the limits|
|:-----|:----------------------------------------------|
|The AIX all-in-one edition is not supported.| |
|Only the following data types are supported: -   SMALLINT, INTEGER, BITINT, DECIMAL, CHAR, VARCHAR, DECFLOAT, and DOUBLE REAL
-   GRAPHIC, VARGRAPHIC \(Data records of the GRAPHIC and VARGRAPHIC types are stored as double-byte strings. The boundary check and validity check are not performed.\), and DECFLOAT \(The data range is large. Some databases do not support this data type. Only numeric values can be truncated.\)

|Data fails to be migrated.|
|The primary/secondary switchover logic supports only the SYNC, NEARSYNC, and ASYNC synchronization modes. **Note:** In the ASYNC mode, if a forced switchover is performed when the primary and secondary databases are not synchronized, data loss may occur on the secondary database. A non-forced switchover is performed after the primary and secondary databases are synchronized.

|If SUPERASYNC is used for synchronization, DTS cannot ensure the continuity of the program during switchover.|

|Limits|Negative effect of failing to follow the limits|
|:-----|:----------------------------------------------|
|You cannot migrate data from MongoDB databases in a cluster architecture. You must migrate each shard of a sharded cluster instance.|N/A|
|Transaction information is not retained.|When transactions are migrated to the destination database, they are converted into a single record.|

|Limits|Negative effect of failing to follow the limits|
|:-----|:----------------------------------------------|
|During incremental data migration, only the following DDL operations can be synchronized: -   ALTER TABLE and ALTER VIEW
-   CREATE FUNCTION, CREATE INDEX, CREATE PROCEDURE, CREATE TABLE, and CREATE VIEW
-   DROP INDEX and DROP TABLE
-   RENAME TABLE
-   TRUNCATE TABLE

|If you perform unsupported DDL operations on the source database, data fails to be migrated.|

|Limits|Negative effect of failing to follow the limits|
|:-----|:----------------------------------------------|
|The database in DRDS instance are created based on ApsaraDB RDS for MySQL instances \(MySQL 5.x\) that you purchased. DTS does not support databases that are created based on ApsaraDB RDS for MySQL instances \(MySQL 8.0\) or ApsaraDB PolarDB for MySQL clusters.|The data migration task cannot be configured.|
|DDL operations cannot be synchronized.|Data fails to be migrated.|

|Limits|Negative effect of failing to follow the limits|
|:-----|:----------------------------------------------|
|During incremental data migration, only the following DDL operations can be synchronized: -   CREATE TABLE \(CREATE TABLE operations for creating partition tables or tables that contain functions cannot be synchronized.\)
-   ALTER TABLE

**Note:** ALTER TABLE operations only include ADD COLUMN, DROP COLUMN, and RENAME COLUMN.

-   CREATE INDEX, DROP TABLE, RENAME TABLE, and TRUNCATE TABLE

|Data fails to be migrated.|

|Limits|Negative effect of failing to follow the limits|
|:-----|:----------------------------------------------|
|During incremental data migration, only the following DDL operations can be synchronized: -   CREATE TABLE \(CREATE TABLE operations for creating partition tables or tables that contain functions cannot be synchronized.\)
-   ALTER TABLE

**Note:** ALTER TABLE operations only include ADD COLUMN, DROP COLUMN, RENAME COLUMN, and ADD INDEX.

-   CREATE INDEX, DROP TABLE, RENAME TABLE, and TRUNCATE TABLE

|Data fails to be migrated.|

|Limits|Negative effect of failing to follow the limits|
|:-----|:----------------------------------------------|
|DDL operations cannot be synchronized during incremental data migration.|Data fails to be migrated.|

|Limits|Negative effect of failing to follow the limits|
|:-----|:----------------------------------------------|
|DDL operations cannot be synchronized during incremental data migration.|Data fails to be migrated.|

|Limits|Negative effect of failing to follow the limits|
|:-----|:----------------------------------------------|
|During full data migration, the destination Redis database \(such as Redis Community Edition and ApsaraDB for Redis Community Edition\) cannot contain data.|The collection class or data in the source and destination databases is inconsistent.|

|Limits|Negative effect of failing to follow the limits|
|:-----|:----------------------------------------------|
|The writing of transactions is not supported.|DTS only ensures eventual consistency between the source and destination databases.|

|Limits|Negative effect of failing to follow the limits|
|:-----|:----------------------------------------------|
|A maximum of 1,024 tables can be migrated.|Data fails to be migrated.|
|AnalyticDB for MySQL is incompatible with MySQL in terms of the processing of some invalid values.|Data fails to be migrated.|
|The maximum length of a column is 16 MB by default.|Data fails to be migrated.|

## Data synchronization mode

The following sections list the limits relating to the data synchronization mode of DTS.

|Limits|Negative effect of failing to follow the limits|
|:-----|:----------------------------------------------|
|Only the following DDL operations can be synchronized: -   ALTER TABLE and ALTER VIEW
-   CREATE FUNCTION, CREATE INDEX, CREATE PROCEDURE, CREATE TABLE, and CREATE VIEW
-   DROP INDEX and DROP TABLE
-   RENAME TABLE
-   TRUNCATE TABL

|If you perform unsupported DDL operations on the source database, data fails to be synchronized.|

|Limits|Negative effect of failing to follow the limits|
|:-----|:----------------------------------------------|
|If the Redis database is deployed in the cluster architecture, you cannot run the FLUSHDB, FLUSHALL, or SWAPDB command during data synchronization.|Data in the source and destination databases is inconsistent.|
|During initial full data synchronization, the destination Redis database \(such as Redis Community Edition and ApsaraDB for Redis Community Edition\) cannot contain data.|The collection class or data in the source and destination databases is inconsistent.|

|Limits|Negative effect of failing to follow the limits|
|:-----|:----------------------------------------------|
|A maximum of 1,024 tables can be synchronized.|Data fails to be synchronized.|
|Only the following DDL operations can be synchronized: CREATE TABLE, DROP TABLE, RENAME TABLE, TRUNCATE TABLE, ADD COLUMN, and DROP COLUMN.|Data fails to be synchronized.|
|Each table to be synchronized must contain a primary key. The value of the primary key cannot be NULL.|Data fails to be synchronized or the data synchronization task fails to be started.|
|AnalyticDB for MySQL is incompatible with MySQL in terms of the processing of some invalid values.|Data fails to be synchronized.|
|The maximum length of a column is 16 MB by default.|Data fails to be synchronized.|

|Limits|Negative effect of failing to follow the limits|
|:-----|:----------------------------------------------|
|Only the following DDL operations can be synchronized: ALTER TABLE and ADD COLUMN.|Data fails to be synchronized.|

