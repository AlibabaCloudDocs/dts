Modify the transfer rate of full data migration 
====================================================================

This topic describes how to modify the transfer rate of full data migration in the Data Transmission Service (DTS) console.

Limits 
---------------------------

To modify the transfer rate of full data migration, you must ensure the correspondence of the source and destination databases.


|                                                               Source database                                                               |                                                                                                                                                                                                                                                Destination database                                                                                                                                                                                                                                                 |
|---------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| * User-created MySQL database   * RDS MySQL              | * User-created MySQL database   * RDS MySQL   * MaxCompute                                                                                                                                                                                                                                                                                                                                      |
| * User-created PostgreSQL database   * RDS PostgreSQL    | * User-created PostgreSQL database   * RDS PostgreSQL                                                                                                                                                                                                                                                                                                                                                                            |
| User-created Oracle database                                                                                                                | * User-created Oracle database   * User-created MySQL database   * RDS MySQL   * User-created PostgreSQL database   * RDS PostgreSQL   * PolarDB-O   * AnalyticDB for MySQL (version 3.0)   * AnalyticDB for PostgreSQL    |
| PolarDB-O                                                                                                                                   | PolarDB-O                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| User-created TiDB database                                                                                                                  | * User-created MySQL database   * RDS MySQL   * PolarDB MySQL                                                                                                                                                                                                                                                                                                                                   |



Procedure 
------------------------------

1. Log on to the [DTS console](https://dts-intl.console.aliyun.com/).

   

2. In the left-side navigation pane, click **Data Migration** .

   

3. At the top of the **Migration Tasks** page, select the region where the data migration instance resides.

   ![Select a region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9840359951/p50439.png)

4. On the **Migration Tasks** page, click the ID of the data migration instance.

   

5. In the left-side navigation pane, choose **Performance Monitoring** \> **Performance of Full Data Migration** .

   

6. In the upper-right corner of the page, click **Adjust full migration rate** .

   

7. Modify the transfer rate of full data migration based on your needs.

   ![Modify the transfer rate of full data migration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8424948951/p108889.png)
   **Note**

   You can click the

   ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8424948951/p108886.png)

   icon to view the details of each parameter.
   

8. Click **OK** .

   



