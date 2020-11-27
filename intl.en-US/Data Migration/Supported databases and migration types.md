# Supported databases and migration types

You can use Data Transmission Service \(DTS\) to migrate data between homogeneous and heterogeneous data sources. Typical scenarios include data migration to Alibaba Cloud, data migration between instances within Alibaba Cloud, and database splitting and scale-out. This section describes the database types, database versions, and migration types that are supported by the data migration feature.

For more information about data migration in various scenarios, see [Overview of data migration scenarios](/intl.en-US/Data Migration/Overview of data migration scenarios.md).

**Note:** The data migration feature can achieve data synchronization in specific scenarios. However, data migration and data synchronization differ in scenarios, supported databases, features, and billing. For more information, see [What are the differences between data migration and data synchronization?]()

## Migration types

|Migration type|Description|
|:-------------|:----------|
|Schema migration|DTS migrates the schemas of the required objects from the source database to the destination database. Tables, views, triggers, and stored procedures can be migrated.

For schema migration between heterogeneous databases, DTS converts the schema syntax based on the syntax of the source and destination databases. For example, it converts the NUMBER data type in Oracle databases into the DECIMAL data type in MySQL databases. |
|Full data migration|DTS migrates historical data of the required objects from the source database to the destination database. If you select only schema migration and full data migration, DTS does not migrate incremental data that was generated in the source database. **Note:** To ensure data consistency, do not write data to the source database during full data migration. To ensure service continuity, you must select **Schema Migration**, **Full Data Migration**, and **Incremental Data Migration** when you configure a data migration task. |
|Incremental data migration|DTS retrieves static snapshots that are used for full data migration from the source database and migrates the snapshot data to the destination database. Then, DTS synchronizes incremental data that was generated in the source database to the destination database. **Note:** During incremental data migration, data is synchronized between the source and destination databases in real time. The migration task does not automatically stop. You must manually stop the migration task. |

## Supported databases and migration types

A user-created MySQL, SQL Server, or Oracle database can be one of the following types:

-   **User-created database with a public IP address**
-   **Database that has no public IP address or port number \(connected over Database Gateway\)**
-   **User-created database that is connected over Cloud Enterprise Network \(CEN\)**
-   **User-created database that is hosted on ECS**
-   **User-created database that is connected over Express Connect, VPN Gateway, or Smart Access Gateway**

|Source database|Destination database|Migration type|
|:--------------|:-------------------|:-------------|
|-   User-created MySQL database

Versions 5.1, 5.5, 5.6, 5.7, and 8.0

-   ApsaraDB RDS for MySQL

All versions


|User-created MySQL database

Versions 5.1, 5.5, 5.6, 5.7, and 8.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|ApsaraDB RDS for MySQL

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|PolarDB for MySQL

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|DRDS

All versions

**Note:** A database in a DRDS instance must be created based on one or more ApsaraDB RDS for MySQL instances. DTS does not support DRDS databases that are created based on PolarDB for MySQL clusters.

|-   Full data migration
-   Incremental data migration |
|HybridDB for MySQL \(Previous name: PetaData\)

All versions

|-   Full data migration
-   Incremental data migration |
|AnalyticDB for MySQL

Versions 2.0 and 3.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|User-created PostgreSQL database

Versions 9.4, 9.5, 9.6, 10.x, 11.x, and 12

|-   Full data migration
-   Incremental data migration |
|User-created Oracle database \(RAC or non-RAC deployment\)

Versions 9i, 10g, 11g, 12c, 18c, and 19c

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|User-created Kafka database

Versions 0.10.1.0 to 1.0.2

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|ApsaraDB RDS for MariaDB TX

Version 10.3

|ApsaraDB RDS for MariaDB TX

Version 10.3

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|ApsaraDB RDS for MySQL

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|User-created MySQL database

Versions 5.1, 5.5, 5.6, 5.7, and 8.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|PolarDB for MySQL

All versions

|PolarDB for MySQL

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|User-created MySQL database

Versions 5.1, 5.5, 5.6, 5.7, and 8.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|ApsaraDB RDS for MySQL

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|AnalyticDB for MySQL

Versions 2.0 and 3.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|PolarDB-O

All versions

|PolarDB-O

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|Versions 9i, 10g, 11g, 12c, 18c, and 19c

User-created Oracle database \(RAC or non-RAC deployment\)

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|-   User-created SQL Server database

Versions 2005, 2008, 2008 R2, 2012, 2014, 2016, and 2017

**Note:**

    -   DTS does not support SQL Server clusters or SQL Server Always On availability groups \(AOAG\).
    -   If the version of the source database is 2005, incremental data migration is not supported.
-   ApsaraDB RDS for SQL Server

Versions 2008, 2008 R2, 2012, 2014, 2016, and 2017

**Note:** If the version of the source database is 2008 or 2008 R2, incremental data migration is not supported.


|User-created SQL Server database

Versions 2005, 2008, 2008 R2, 2012, 2014, 2016, and 2017

**Note:** DTS does not support SQL Server clusters or SQL Server Always On availability groups \(AOAG\).

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|ApsaraDB RDS for SQL Server

Versions 2008, 2008 R2, 2012, 2014, 2016, and 2017

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|User-created Oracle database \(RAC or non-RAC deployment\)

Versions 9i, 10g, 11g, 12c, 18c, and 19c

|User-created Oracle database \(RAC or non-RAC deployment\)

Versions 9i, 10g, 11g, 12c, 18c, and 19c

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|PolarDB-O

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|ApsaraDB RDS for PPAS

Versions 9.3 and 10

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|User-created MySQL database

Versions 5.1, 5.5, 5.6, 5.7, and 8.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|ApsaraDB RDS for MySQL

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|PolarDB for MySQL

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|DRDS

All versions

**Note:** A database in a DRDS instance must be created based on one or more ApsaraDB RDS for MySQL instances. DTS does not support DRDS databases that are created based on PolarDB for MySQL clusters.

|-   Full data migration
-   Incremental data migration |
|AnalyticDB for MySQL

Versions 2.0 and 3.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|AnalyticDB for PostgreSQL|-   Schema migration
-   Full data migration
-   Incremental data migration |
|-   User-created PostgreSQL database

Versions 9.4, 9.5, 9.6, 10.x, 11.x, and 12

-   ApsaraDB RDS for PostgreSQL

Versions 9.4, 10, 11, and 12


|-   User-created PostgreSQL database

Versions 9.4, 9.5, 9.6, 10.x, 11.x, and 12

-   ApsaraDB RDS for PostgreSQL

Versions 9.4, 10, 11, and 12


|-   Schema migration
-   Full data migration
-   Incremental data migration |
|-   User-created MongoDB database \(standalone, replica set, or sharded cluster deployment\)

Versions 3.0, 3.2, 3.4, 3.6, and 4.0

-   ApsaraDB for MongoDB instance \(standalone or replica set deployment\)

All versions


|-   User-created MongoDB database \(standalone, replica set, or sharded cluster deployment\)

Versions 3.0, 3.2, 3.4, 3.6, and 4.0

-   ApsaraDB for MongoDB instance \(standalone, replica set, or sharded cluster deployment\)

All versions


|-   Full data migration
-   Incremental data migration

**Note:** MongoDB is a NoSQL database that does not require schema migration. |
|User-created Redis database \(standalone deployment\)

Versions 2.8, 3.0, 3.2, 4.0, and 5.0

|User-created Redis database \(standalone or cluster deployment\)

Versions 2.8, 3.0, 3.2, 4.0, and 5.0

|-   Full data migration
-   Incremental data migration

**Note:** Redis is a NoSQL database that does not require schema migration. |
|ApsaraDB for Redis instance \(standalone or cluster deployment\)

Community versions 4.0 and 5.0

|-   Full data migration
-   Incremental data migration |
|User-created TiDB database|User-created MySQL database

Versions 5.1, 5.5, 5.6, 5.7, and 8.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|ApsaraDB RDS for MySQL

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|PolarDB for MySQL

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|User-created Db2 database

Versions 9.7 and 10.5

|User-created MySQL database

Versions 5.1, 5.5, 5.6, 5.7, and 8.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |

