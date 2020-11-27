# Data type mappings for initial schema synchronization

The data types of different databases do not have one-to-one correspondence. During initial schema synchronization, Data Transmission Service \(DTS\) maps the data types of the source database to those of the destination database. The following tables list the data type mappings for you to view and evaluate the impact of data synchronization on your business.

## Data synchronization from MySQL, ApsaraDB RDS for MySQL, or PolarDB for MySQL to AnalyticDB for MySQL

|MySQL data type|AnalyticDB for MySQL data type|
|---------------|------------------------------|
|bigint unsigned|decimal\(20,0\)|
|binary|VARBINARY|
|bit|VARCHAR|
|blob|VARBINARY|
|char|varchar|
|date|DATE|
|datetime|DATETIME|
|decimal|decimal|
|double|double|
|enum|VARCHAR|
|FLOAT|FLOAT|
|geometry|VARBINARY|
|geometrycollection|VARBINARY|
|int unsigned|BIGINT|
|integer|int|
|json|JSON|
|linestring|VARBINARY|
|LONGBLOB|VARBINARY|
|LONGTEXT|varchar|
|MEDIUMBLOB|VARBINARY|
|mediumint|int|
|mediumint unsigned|INT|
|MEDIUMTEXT|varchar|
|multilinestring|VARBINARY|
|multipoint|VARBINARY|
|multipolygon|VARBINARY|
|numberic|DECIMAL|
|point|VARBINARY|
|polygon|VARBINARY|
|set|VARCHAR|
|smallint unsigned|INT|
|text|VARCHAR|
|time|TIME|
|timestamp|TIMESTAMP|
|TINYBLOB|VARBINARY|
|tinyint unsigned|SMALLINT|
|TINYTEXT|varchar|
|VARBINARY|VARBINARY|
|varchar|varchar|
|year|INT|

## Data synchronization from MySQL, ApsaraDB RDS for MySQL, or PolarDB for MySQL to Elasticsearch

|MySQL data type|Elasticsearch data type|Description|
|:--------------|:----------------------|:----------|
|BIGINT|LONG|None.|
|BINARY|BINARY|None.|
|BIT|LONG|If the data is only one byte long, we recommend that you use the BOOLEAN type in Elasticsearch.|
|BLOB|BINARY|None.|
|BOOL/BOOLEAN|BOOLEAN|None.|
|CHAR|TEXT|None.|
|DATE|DATE|The DATE format is yyyy-MM-dd, in UTC. For more information, see [Date format mappings](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-date-format.html).|
|DATETIME|DATE|The DATE format is yyyy-MM-dd'T'HH:mm:ss, in UTC. If DATE is accurate to microseconds, its format is yyyy-MM-dd'T'HH:mm:ss.S. For more information, see [Date format mappings](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-date-format.html).|
|DECIMAL/DEC|DOUBLE|If the DECIMAL value contains a decimal point, we recommend that you use the TEXT type in Elasticsearch to ensure data consistency.|
|DOUBLE|DOUBLE|None.|
|FLOAT|FLOAT|None.|
|GEOMETRY|GEO\_SHAPE|None.|
|GEOMETRYCOLLECTION|GEO\_SHAPE|None.|
|INT|INTEGER|If the data type of the source database is UNSIGNED INT, we recommend that you use the LONG type in Elasticsearch.|
|JSON|OBJECT|If the data is only one byte long, we recommend that you use the BOOLEAN type in Elasticsearch.|
|LINESTRING|GEO\_SHAPE|None.|
|LONGTEXT|TEXT|None.|
|MEDIUMINT|INTEGER|None.|
|MEDIUMTEXT|TEXT|None.|
|MULTILINESTRING|GEO\_SHAPE|None.|
|MULTIPOINT|GEO\_SHAPE|If the data is only one byte long, we recommend that you use the BOOLEAN type in Elasticsearch.|
|MULTIPOLYGON|GEO\_SHAPE|None.|
|POINT|GEO\_POINT|None.|
|POLYGON|GEO\_SHAPE|None.|
|REAL|DOUBLE|None.|
|SMALLINT|SHORT|If the data type of the source database is UNSIGNED SMALLINT, we recommend that you use the INTEGER type in Elasticsearch.|
|TIME|DATE|The DATE format is HH:mm:ss, in UTC. If DATE is accurate to microseconds, its format is HH:mm:ss.S. For more information, see [Date format mappings](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-date-format.html).|
|TIMESTAMP|DATE|The DATE format is yyyy-MM-dd'T'HH:mm:ss, in UTC. If DATE is accurate to microseconds, its format is yyyy-MM-dd'T'HH:mm:ss.S. For more information, see [Date format mappings](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-date-format.html).|
|TINYINT|SHORT|If the data type of the source database is UNSIGNED TINYINT, we recommend that you use the INTEGER type in Elasticsearch.|
|TINYTEXT|TEXT|None.|
|VARCHAR|TEXT|None.|
|YEAR|DATE|The DATE format is yyyy, in UTC. For more information, see [Date format mappings](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-date-format.html).|

