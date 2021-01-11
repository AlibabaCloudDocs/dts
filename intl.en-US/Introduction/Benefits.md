# Benefits

Alibaba Cloud Data Transmission Service \(DTS\) helps you migrate data between data stores, such as relational databases, NoSQL databases, and data warehouses. You can use DTS to migrate your data to Alibaba Cloud or between combinations of cloud and on-premises data systems.

As a managed service, DTS offers several advantages compared with a traditional data migration tool. DTS runs with better performance and guaranteed availability and works seamlessly with Alibaba Cloud database services.

## Compatibility with diverse database engines

DTS supports heterogeneous migrations, such as Oracle to MySQL and Oracle to PolarDB-O. To reconcile the differences between database engines, DTS provides schema conversion. For example, you can convert an Oracle synonym to a PolarDB-O synonym.

## Support for multiple use cases

DTS supports several data replication modes, including data migration, data synchronization, and change tracking. You can choose a combination of data replication modes that best suit your use cases.

You can use the data synchronization mode to enable ongoing replication between two data stores and keep them in sync. The replication can work either one-way or two-way.

**Note:** Two-way synchronization is only supported for synchronization between two MySQL databases, two PolarDB for MySQL databases, and two ApsaraDB for Redis Enhanced Edition \(Tair\) databases.

The data synchronization mode allows you to build a distributed system with nodes that replicate to each other in real time, so that you implement high availability, load balancing, and real-time data warehousing based on this setup.

## Minimized-downtime migrations

The data migration mode can migrate your data with minimized downtimes. Your source database can remain operational during migration. The actual downtime during data migration is reduced to minutes.

## Superior performance

DTS provisions high-performance servers to support data replication workloads. In addition, DTS has introduced a myriad of optimizations to the core infrastructure so that it can deliver a peak data transmission rate of 70 MB per second.

The data synchronization mode can replicate ongoing updates with ultra-low latency. The replication works at transaction level so that even updates to the same table can be broken down to transactions and processed in parallel. This technique significantly improves the concurrency of replication workloads and helps DTS deliver a peak rate of 30,000 records per second \(RPS\).

DTS supports multiple concurrency and compressed data transmission to reduce bandwidth usage.

**Note:** The performance measurements listed here are only benchmarking references. The actual data replication performance depends on multiple factors, such as the performance of the source and target databases, network conditions, and your provisioned instance size.

## High availability

DTS runs on high-availability deployments, where each cluster has multiple server nodes. If one node fails, the HA manager switches the workloads to a healthy node in the cluster within seconds.

For certain critical replication links, DTS continuously performs validation to check for data integrity and makes corrections automatically.

The data flows between different functions of DTS are secured and resumable.

## Ease of use

The Data Transmission Service console provides an intuitive wizard for you to create replication tasks with a few clicks.

You can easily monitor your replication tasks and view key statistics, such as replication status, progress, and performance.

DTS continuously monitors replication task status. If DTS detects a network or system failure, it automatically fixes the issue, restarts the task, and resumes from where it left off. If the issue persists, you can manually repair and restart the task in the DTS console.

