# Source databases and data change types

You can use Data Transmission Service \(DTS\) to track data changes from user-created MySQL databases, and ApsaraDB RDS for MySQL instances in real time. This feature applies to the following scenarios: cache updates, business decoupling, synchronization of heterogeneous data, and synchronization of extract, transform, and load \(ETL\) operations.

## Change tracking \(previous\)

|Source database|Data change type|
|---------------|----------------|
|ApsaraDB RDS for MySQL

Versions 5.5, 5.6, and 5.7

|-   Data update

Data updates are operations that modify data, such as INSERT, DELETE, and UPDATE operations.

-   Schema update

Schema updates are operations that modify the schema syntax, such as CREATE TABLE and ALTER TABLE operations. |

## Change tracking \(new\)

The previous change tracking feature requires high costs. To improve user experience and reduce costs, Alibaba Cloud has upgraded the change tracking feature to achieve repeated consumption of data across multiple downstream instances. For more information, see [Change tracking \(new\)]().

**Note:** A source MySQL database can be one of the following types: **User-Created Database with Public IP Address**, **User-Created Database in ECS Instance**, and **User-Created Database Connected Over Express Connect, VPN Gateway, or Smart Access Gateway**.

|Source database|Data change type|
|---------------|----------------|
|User-created MySQL database

Versions 5.1, 5.5, 5.6, 5.7, and 8.0

|-   Data update

Data updates are operations that modify data, such as INSERT, DELETE, and UPDATE operations.

-   Schema update

Schema updates are operations that modify the schema syntax, such as CREATE TABLE and ALTER TABLE operations. |
|ApsaraDB RDS for MySQLAll versions |

