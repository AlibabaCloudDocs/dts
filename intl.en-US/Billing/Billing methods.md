# Billing methods

This topic describes the payment methods, billing methods, and billable items of Data Transmission Service \(DTS\).

## Pricing

For details about how DTS is priced, see [DTS pricing](https://www.alibabacloud.com/product/data-transmission-service/pricing).

## Payment methods

You can use the following methods to pay for DTS:

-   Bank card
-   PayPal

    Alibaba Cloud pre-authorizes your PayPal account after your pay-as-you-go resources start to incur fees.

-   Paytm \(India\)

    Only for users in India. Alibaba Cloud pre-authorizes your Paytm account after your pay-as-you-go resources start to incur fees. Note Coupons are used to pay for your resource usage before bills are issued. No actual payments are involved.


## Billing methods

DTS provides two billing methods for you to choose from: subscription and pay-as-you-go. Note that data migration mode instances only support pay-as-you-go.

|Billing method|Supported modes|Description|
|--------------|---------------|-----------|
|Subscription|-   Data synchronization
-   Change tracking

|-   You pay for your subscription when creating an instance. Subscription lengths are monthly or yearly.
-   If you plan to use DTS for a month or longer, we recommend that you select this billing method because it will be more cost effective.
-   You cannot release a subscription instance. If the subscription is not renewed, the instance expires at the end of the subscription period.
-   You cannot switch the billing method of an instance from subscription to pay-as-you-go. |
|Pay-as-you-go|-   Data migration
-   Data synchronization
-   Change tracking

|-   Your instances are billed based on the time your tasks are running. A pay-as-you-go instance is billed on an hourly basis. The hourly fee is calculated based on the instance size and you are charged by using your chosen billing method.
-   You can release a pay-as-you-go instance at any time. For more information, see [Release pay-as-you-go instances](/intl.en-US/Instance Management/Release pay-as-you-go instances.md).
-   We recommend that you select this billing method for short term use.
-   You can switch a pay-as-you-go instance to a subscription instance. For more information, see [Switch from pay-as-you-go to subscription](/intl.en-US/Billing/Switch from pay-as-you-go to subscription.md).

**Note:**

    -   For data synchronization and data migration tasks, you will be billed during the period when the tasks are paused.
    -   When data synchronization or incremental data migration is paused, DTS temporarily stops writing to the target database but continues to read logs from the source database. This helps to quickly resume data synchronization or migration once the task is resumed. |

## Billable items

|Data replication mode|Billable item|Billing rules|
|---------------------|-------------|-------------|
|Data migration|Instance size|-   You are only billed when incremental data migration is in progress \(including the period when the incremental data migration is paused\). You are not billed for schema migration and full data migration.

**Note:** If data migration fails, you will not be charged during the period in which the migration has a failed status.

-   Within one hour before an overdue payment occurs, if you have used the service for no more than half an hour, you are billed for half an hour of service usage. If you have used the service for more than half an hour, you are billed for one hour of service usage. |
|Internet traffic charges|DTS involves two types of public network traffic: inbound traffic \(inbound traffic to Alibaba Cloud services\) and outbound traffic \(outbound traffic from Alibaba Cloud services\).-   Outbound traffic: You are billed for the outbound traffic that is generated, for example, when you migrate a user-created database on an ECS instance or an Alibaba Cloud database to an on-premises database over the public network.
-   Inbound traffic: You are not billed for the inbound traffic that is generated, for example, when you migrate an on-premises database to a user-created database on an ECS instance or a managed database service on Alibaba Cloud over the public network.

**Note:** Internet traffic charges are based on the actual traffic that is generated. Traffic of less than 1 GB is charged as 1 GB. |
|Data synchronization|Instance size|-   Pay-as-you-go

Billing starts when the task is started. \(A period during which the task is paused is also billed.\)

Within one hour before an overdue payment occurs, if you have used the service for no more than half an hour, you are billed for half an hour of service usage. If you have used the service for more than half an hour but less than one hour, you are billed for one hour of service usage.

-   Subscription

You must pay for your subscription based on the instance size, subscription period, and number of instances. |
|Change tracking|Instance size|-   Pay-as-you-go

Billing starts when the task is started.

Within one hour before an overdue payment occurs, if you have used the service for no more than half an hour, you are billed for half an hour of service usage. If you have used the service for more than half an hour, you are billed for one hour of service usage.

-   Subscription

You must pay for your subscription based on the instance size, subscription period, and number of instances. |
|Amount of subscribed data|Free|
|Internet traffic charges|Free|

