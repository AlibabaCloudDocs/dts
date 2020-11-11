# Data type mappings between heterogeneous databases

Heterogeneous databases support different data types. During schema migration, Data Transmission Service \(DTS\) converts the data types of the source database into those of the destination database. This topic lists the data type mappings for you to evaluate the impact of data migration on your business.

For more information about data migration scenarios, see [Overview of data migration scenarios](/intl.en-US/Data Migration/Overview of data migration scenarios.md).

## Data migration from a user-created TiDB database to a user-created MySQL database, an ApsaraDB RDS for MySQL instance, or a PolarDB for MySQL cluster

|TiDB data type|MySQL data type|
|--------------|---------------|
|BIGINT|BIGINT|
|BINARY|BINARY|
|BIT|BIT|
|BOOL\\ BOOLEAN|TINYINT|
|CHAR|CHAR|
|DATE|DATE|
|DATETIME|DATETIME|
|DECIMAL|DECIMAL|
|DOUBLE|DOUBLE|
|ENUM|ENUM|
|FLOAT|FLOAT|
|INT|INT|
|INTEGER|INTEGER|
|JSON|JSON|
|MEDIUMBLOB/LONGBLOB

 TINYBLOB/BLOB/

|MEDIUMBLOB/LONGBLOB

 TINYBLOB/BLOB/ |
|MEDIUMINT|MEDIUMINT|
|SET|SET|
|SMALLINT|SMALLINT|
|TEXT/LONGTEXT|TEXT/LONGTEXT|
|TIME|TIME|
|TIMESTAMP|TIMESTAMP|
|TINYINT|TINYINT|
|VARBINARY|VARBINARY|
|VARCHAR|VARCHAR|
|YEAR|YEAR|

## Data migration from a user-created Oracle database to a user-created MySQL database, an ApsaraDB RDS for MySQL instance, or a PolarDB for MySQL cluster

|Oracle data type|MySQL data type|
|:---------------|:--------------|
|varchar2\(n \[char/byte\]\)|varchar\(n\)|
|nvarchar2\[\(n\)\]|national varchar\[\(n\)\]|
|char\[\(n \[byte/char\]\)\]|char\[\(n\)\]|
|nchar\[\(n\)\]|national char\[\(n\)\]|
|number\[\(p\[,s\]\)\]|decimal\[\(p\[,s\]\)\]|
|float\(p\)\]|double|
|long|longtext|
|date|datetime|
|binary\_float|decimal\(65,8\)|
|binary\_double|double|
|timestamp\[\(fractional\_seconds\_precision\)\]|datetime\[\(fractional\_seconds\_precision\)\]|
|timestamp\[\(fractional\_seconds\_precision\)\]with localtimezone|datetime\[\(fractional\_seconds\_precision\)\]|
|timestamp\[\(fractional\_seconds\_precision\)\]with localtimezone|datetime\[\(fractional\_seconds\_precision\)\]|
|clob|longtext|
|nclob|longtext|
|blob|longblob|
|raw|varbinary\(2000\)|
|long raw|longblob|
|bfile|Not supported|
|interval year\(year\_precision\) to month|Not supported|
|interval day\(day\_precision\)to second\[\(fractional\_seconds\_precision\)\]|Not supported|

**Note:**

-   If a char field in the source database is greater than 255 bytes in length, DTS converts this field to the varchar\(n\) type in the destination database.
-   MySQL does not support the following Oracle data types: bfile, interval year to month, and interval day to second. Therefore, DTS does not convert these data types during schema migration.

    If the table to be migrated contains these data types, the schema migration fails. You must make sure that the columns with these data types are excluded from the objects to be migrated.

-   The timestamp data type of MySQL databases does not contain the time zone information. However, the timestamp with time zone and timestamp with local time zone data types in Oracle databases provide the time zone information. Therefore, DTS converts the values of these data types into UTC time in the destination database.

## Data migration from a user-created Oracle database to a DRDS instance

|Oracle data type|DRDS data type|
|:---------------|:-------------|
|varchar2\(n \[char/byte\]\)|varchar\(n\)|
|nvarchar2\[\(n\)\]|national varchar\[\(n\)\]|
|char\[\(n \[byte/char\]\)\]|char\[\(n\)\]|
|nchar\[\(n\)\]|national char\[\(n\)\]|
|number\[\(p\[,s\]\)\]|decimal\[\(p\[,s\]\)\]|
|float\(p\)\]|double|
|long|longtext|
|date|datetime|
|binary\_float|decimal\(65,8\)|
|binary\_double|double|
|timestamp\[\(fractional\_seconds\_precision\)\]|datetime\[\(fractional\_seconds\_precision\)\]|
|timestamp\[\(fractional\_seconds\_precision\)\]with localtimezone|datetime\[\(fractional\_seconds\_precision\)\]|
|timestamp\[\(fractional\_seconds\_precision\)\]with localtimezone|datetime\[\(fractional\_seconds\_precision\)\]|
|clob|longtext|
|nclob|longtext|
|blob|longblob|
|raw|varbinary\(2000\)|
|long raw|longblob|
|bfile|Not supported|
|interval year\(year\_precision\) to month|Not supported|
|interval day\(day\_precision\)to second\[\(fractional\_seconds\_precision\)\]|Not supported|

**Note:**

-   If a char field in the source database is greater than 255 bytes in length, DTS converts this field to the varchar\(n\) type in the DRDS instance.
-   The timestamp data type of DRDS instances does not contain the time zone information. However, the timestamp with time zone and timestamp with local time zone data types in Oracle databases provide the time zone information. Therefore, DTS converts the values of these data types into UTC time in the destination DRDS instance.

## Data migration from a user-created Oracle database to an ApsaraDB RDS for PPAS instance

|Oracle data type|PPAS data type|
|:---------------|:-------------|
|varchar2\(n \[char/byte\]\)|varchar2\[\(n\)\]|
|nvarchar2\[\(n\)\]|nvarchar2\[\(n\)\]|
|char\[\(n \[byte/char\]\)\]|char\[\(n\)\]|
|nchar\[\(n\)\]|nchar\[\(n\)\]|
|number\[\(p\[,s\]\)\]|number\[\(p\[,s\]\)\]|
|float\(p\)\]|double precision|
|long|long|
|date|date|
|binary\_float|real|
|binary\_double|double precision|
|timestamp\[\(fractional\_seconds\_precision\)\]|timestamp\[\(fractional\_seconds\_precision\)\]|
|timestamp\[\(fractional\_seconds\_precision\)\]with time zone|timestamp\[\(fractional\_seconds\_precision\)\]with time zone|
|timestamp\[\(fractional\_seconds\_precision\)\]with local time zone|timestamp\[\(fractional\_seconds\_precision\)\]with time zone|
|clob|clob|
|nclob|nclob|
|blob|blob|
|raw|raw\(size\)|
|long raw|long raw|
|bfile|Not supported|
|interval year\(year\_precision\) to month|Not supported|
|interval day\(day\_precision\) to second\[\(fractional\_seconds\_precision\)\]|Not supported|

**Note:** ApsaraDB RDS for PPAS does not support the timestamp\[\(fractional\_seconds\_precision\)\]with local time zone data type. DTS converts the data of this type into UTC time and then stores the data in the destination ApsaraDB RDS for PPAS instance by using the timestamp\[\(fractional\_seconds\_precision\)\]with time zone data type.

## Data migration from a user-created Oracle database to an AnalyticDB for PostgreSQL instance

|User-created Oracle database| |
|----------------------------|--|
|VARCHAR2|varchar/text|
|BINARY\_DOUBLE|double precision|
|BINARY\_FLOAT|double precision|
|BINARY\_INTEGER|integer|
|BLOB|bytea|
|CLOB|text|
|DATE|timestamp|
|DEC|decimal|
|DECIMAL|decimal|
|DOUBLE PRECISION|double precision|
|FLOAT|double precision|
|INT|int|
|INTERGE|integer|
|LONG|text|
|LONG RAW|bytea|
|NCLOB|text|
|NUMBER|numeric|
|PLS\_INTEGER|integer|
|RAW|bytea|
|REAL|real|
|ROWID|oid|
|SMALLINT|smallint|
|TIMESTAMP|timestamp|
|TIMESTAMP WITH LOCAL TIME ZONE|timestamp with time zone|
|TIMESTAMP WITH TIME ZONE|timestamp with time zone|
|XMLTYPE|xml|

**Note:** If an Oracle data type is not supported by , DTS converts the data type into bytea. If the conversion fails, DTS sets the field value to null.

