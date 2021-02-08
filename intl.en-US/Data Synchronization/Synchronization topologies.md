# Synchronization topologies

The data synchronization feature supports multiple types of synchronization topologies. You can select a topology for your data synchronization instances based on your business requirements. This topic describes the synchronization topologies that are supported by Data Transmission Service \(DTS\) and how to use these topologies.

## One-way synchronization

To ensure data consistency for one-way synchronization, we recommend that you perform only read operations on the objects in the destination instance. Do not perform write operations on the objects.

|Topology type|Topology|Description|
|:------------|:-------|:----------|
|One-way one-to-one synchronization|![One-way one-to-one synchronization](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2108539951/p51087.png)

|None|
|One-way one-to-many synchronization|![One-way one-to-many synchronization](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2108539951/p51088.png)

|You must purchase multiple data synchronization instances to implement one-way one-to-many synchronization.

 For example, if you want to synchronize data from Instance A to Instance B, Instance C, and Instance D, you must purchase three data synchronization instances. |
|One-way cascade synchronization|![One-way cascade synchronization](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2108539951/p51089.png)

|You must purchase multiple data synchronization instances to implement one-way cascade synchronization.

 For example, if you want to synchronize data from Instance A to Instance B and then from Instance B to Instance C, you must purchase two data synchronization instances. |
|One-way many-to-one synchronization|![One-way many-to-one synchronization](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2108539951/p51090.png)

|You must purchase multiple data synchronization instances to implement one-way many-to-one synchronization.

 For example, if you want to synchronize data from Instance B, Instance C, and Instance D to Instance A, you must purchase three data synchronization instances.

 **Note:** To ensure data consistency, you must select different objects for these data synchronization instances. |

## Two-way synchronization

DTS supports two-way data synchronization only between two MySQL databases. DTS does not support two-way data synchronization between multiple MySQL databases.

**Note:**

-   For more information about how to configure two-way synchronization, see [Configure two-way data synchronization between ApsaraDB RDS for MySQL instances](/intl.en-US/Data Synchronization/Synchronize data between MySQL databases/Configure two-way data synchronization between ApsaraDB RDS for MySQL instances.md).
-   You can upgrade the synchronization topology of a data synchronization task from one-way to two-way. For more information, see [t17079.md\#](/intl.en-US/Instance Management/Upgrade synchronization topology from one-way to two-way.md).

|Topology type|Topology|Description|
|:------------|:-------|:----------|
|Two-way one-to-one synchronization|![Two-way data synchronization](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7920359951/p41047.png)

|To ensure data consistency, make sure that records with the same primary key, business primary key, or unique key are updated only on one of the instances.

**Note:** If data records are updated on both instances, DTS responds to conflicts based on the conflict resolution policy that you specify for the data synchronization task. |

## References

-   [Overview of data synchronization scenarios](/intl.en-US/Data Synchronization/Overview of data synchronization scenarios.md)
-   [Database types, initial synchronization types, and synchronization topologies](/intl.en-US/Data Synchronization/Database types, initial synchronization types, and synchronization topologies.md)

