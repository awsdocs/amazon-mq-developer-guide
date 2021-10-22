# Maintaining an Amazon MQ broker<a name="maintaining-brokers"></a>

 Periodically, Amazon MQ performs maintenance to the hardware, operating system, or the engine software a message broker\. The duration of the maintenance varies, but can last up to two hours, depending on the operations that are scheduled for your message broker\. For example, if you've activated [automatic minor engine version upgrades](upgrading-brokers.md#upgrading-brokers-automatic-upgrades), or changed the broker instance type, Amazon MQ will apply your changes during the next scheduled maintenance window\.

To minimize downtime during a maintenance window, we recommend selecting a broker deployment mode with high availability across multiple Availability Zones \(AZ\)\. Depending on your broker engine type, Amazon MQ provides the following Multi\-AZ deployment modes\.
+  **Amazon MQ for ActiveMQ** – Amazon MQ for ActiveMQ provides [active/standby](active-standby-broker-deployment.md) deployments for high availability\. In active/standby mode, Amazon MQ performs maintenance operations one instance at a time, ensuring that at least one instance remains available\. In addition, you can configure a [network of brokers](network-of-brokers.md) with maintenance windows scattered across the week\. 
+  **Amazon MQ for RabbitMQ** – Amazon MQ for RabbitMQ provides the [cluster](rabbitmq-broker-architecture-cluster.md) deployments for high availability\. In cluster deployments, Amazon MQ performs maintenance operations, one node at a time, keeping at least two running nodes at all times\. 

 For more information about Amazon MQ recommended best practices to ensure your brokers perform effectively during, and after a maintenance window, see the following documentation for your broker engine type\. 
+ [Amazon MQ for ActiveMQ best practices](best-practices-activemq.md)
+ [Amazon MQ for RabbitMQ best practices](best-practices-rabbitmq.md)

 You can schedule maintenance to occur once a week at a specified time which lasts up to two hours\. This sets the window for maintenance actions from Amazon MQ to be scheduled and started\.

You can schedule the maintenance window when you first create your broker, or by updating your broker preferences\. The following topic describes adjusting the broker maintenance window using the AWS Management Console, AWS CLI, and the Amazon MQ API\.

**Topics**
+ [Adjusting the broker maintenance window](#maintaining-brokers-adjusting-maintenance-window)

## Adjusting the broker maintenance window<a name="maintaining-brokers-adjusting-maintenance-window"></a>

 To adjust the broker maintenance window, you can use the AWS Management Console, the AWS CLI, or the Amazon MQ API\. 

**Important**  
 You can only adjust the maintenance window of a broker up to **four** times before the next scheduled maintenance window\. Amazon MQ applies a limit of four maintenance window adjustments to ensure that critical software and security patches, as well as important hardware upgrades, are not indefinitely deferred and postponed\.   
 Once a broker maintenance window is completed, Amazon MQ resets the limit, allowing you to adjust the schedule before the next maintenance window occurs\. 

### AWS Management Console<a name="maintaining-brokers-adjusting-maintenance-window-console"></a>

**To adjust the broker maintenance window by using the AWS Management Console**

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. In the left navigation pane, choose **Brokers**, and then choose the broker that you want to upgrade from the list\.

1.  On the broker details page, choose **Edit**\. 

1. Under **Maintenance**, do the following\.

   1.  For **Start day**, choose a day of the week, for example, **Sunday**, from the drop\-down list\. 

   1.  For **Start time**, choose the hour and minute of the day that you want to schedule for the next broker maintenance window, for example, **12**:**00**\. 
**Note**  
 The **Start time** options are configured in UTC\+0 time zone\. 

1. Scroll to the bottom of the page, and choose **Save**\. The maintenance window is adjusted immediately\.

1. On the broker details page, under **Maintenance window**, verify that your new preferred schedule is displayed\.

### AWS CLI<a name="maintaining-brokers-adjusting-maintenance-window-cli"></a>

**To adjust the broker maintenance window using the AWS CLI**

1.  Use the [update\-broker](https://docs.aws.amazon.com/cli/latest/reference/mq/update-broker.html) CLI command and specify the following parameters, as shown in the example\. 
   +  `--broker-id` – The unique ID that Amazon MQ generates for the broker\. You can parse the ID from your broker ARN\. For example, given the following ARN, `arn:aws:mq:us-east-2:123456789012:broker:MyBroker:b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`, the broker ID would be `b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`\. 
   +  `--maintenance-window-start-time` – The parameters that determine the weekly maintenance window start time provided in the following structure\. 
     + `DayOfWeek` – The day of the week, in the following syntax: `MONDAY| TUESDAY | WEDNESDAY | THURSDAY | FRIDAY | SATURDAY | SUNDAY`
     + `TimeOfDay` – The time, in 24\-hour format\.
     + `TimeZone` – \(Optional\) The time zone, in either the Country/City, or the UTC offset format\. Set to UTC by default\.

   ```
   aws mq update-broker --broker-id broker-id \
   --maintenance-window-start-time DayOfWeek=SUNDAY,TimeOfDay=13:00,TimeZone=America/Los_Angeles
   ```

1.  \(Optional\) Use the [describe\-broker](https://docs.aws.amazon.com/cli/latest/reference/mq/reboot-broker.html) CLI command to verify that the maintenance window is successfully updated\. 

   ```
   aws mq describe-broker --broker-id broker-id
   ```

### Amazon MQ API<a name="maintaining-brokers-adjusting-maintenance-window-api"></a>

**To adjust the broker maintenance window using the Amazon MQ API**

1.  Use the [UpdateBroker](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id.html#UpdateBroker) API operation\. Specify `broker-id` as a path parameter\. The following examples assumes a broker in the `us-west-2` region\. For more information about available Amazon MQ endpoints, see [Amazon MQ endpoints and quotas\.](https://docs.aws.amazon.com/general/latest/gr/amazon-mq.html#amazon-mq_region) in the *AWS General Reference* 

   ```
   PUT /v1/brokers/broker-id HTTP/1.1
   Host: mq.us-west-2.amazonaws.com
   Date: Wed, 7 July 2021 12:00:00 GMT
   x-amz-date: Wed, 7 July 2021 12:00:00 GMT
   Authorization: authorization-string
   ```

   Use the `maintenanceWindowStartTime` parameter and the [ `WeeklyStartTime`](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id.html#brokers-broker-id-model-weeklystarttime) resource type in the request payload\.

   ```
   {
   "maintenanceWindowStartTime": {
       "dayOfWeek": "SUNDAY",
       "timeZone": "America/Los_Angeles",
       "timeOfDay": "13:00"
     }
   }
   ```

1.  \(Optional\) Use the [DescribeBroker](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id.html#brokers-broker-id-http-methods) API operation to verify that the maintenance window has been successfully updated\. `broker-id` is specified as a path parameter\. 

   ```
   GET /v1/brokers/broker-id HTTP/1.1
   Host: mq.us-west-2.amazonaws.com
   Date: Wed, 7 July 2021 12:00:00 GMT
   x-amz-date: Wed, 7 July 2021 12:00:00 GMT
   Authorization: authorization-string
   ```