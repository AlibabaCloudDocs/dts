# Use SQL conditions to filter data

When you select the objects to be synchronized in a data synchronization task, you can specify SQL conditions to filter data. Only the data that meets the specified conditions is synchronized to the destination database. This feature is applicable to scenarios such as regular data synchronization and table partitioning.

## Prerequisites

A data synchronization task is configured. The current step is **Select Objects to Synchronize**. For more information about how to create and configure a data synchronization task, see [Create a data synchronization task]().

## Limits

You can filter only the fields in the current table. Cross-table filtering is not supported.

## Procedure

1.  In the **Select Objects to Synchronize** step, move the required objects to the Selected section, move the pointer over a table, and then click **Edit**.

    ![Select a table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0700398951/p51519.png)

2.  In the **Edit Table** dialog box, enter an SQL condition in the **Filter** field.

    ![Enter an SQL condition](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0700398951/p49114.png)

    **Note:**

    -   An SQL condition is a standard SQL WHERE statement. The following operators are supported: `=`, `!=`, `<`, `>`, and `in`. Only the data that meets the WHERE condition is synchronized to the destination database. In this example, enter `orderid>100`.
    -   You can use apostrophes \('\) in an SQL condition if necessary. For example, you can enter `address in('hangzhou','shanghai')`.
3.  Click **Verify** to check whether the syntax is valid.

    **Note:**

    -   If the syntax is valid, the Information message prompts that **the validation is passed**.
    -   If the syntax is invalid, the Error message appears and you must modify the SQL WHERE statement based on the instructions.
4.  Click **OK**.
5.  Configure other parameters that are required for the data synchronization task.

