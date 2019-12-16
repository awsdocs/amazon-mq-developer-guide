# Limits in Amazon MQ<a name="amazon-mq-limits"></a>

This topic lists limits within Amazon MQ\. Many of the following limits can be changed for specific AWS accounts\. To request an increase for a limit, see [AWS Service Limits](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) in the *Amazon Web Services General Reference*\.

**Topics**
+ [Limits Related to Brokers](#broker-limits)
+ [Limits Related to Configurations](#configuration-limits)
+ [Limits Related to Users](#activemq-user-limits)
+ [Limits Related to Data Storage](#data-storage-limits)
+ [Limits Related to API Throttling](#api-throttling-limits)

## Brokers<a name="broker-limits"></a>

The following table lists limits related to Amazon MQ brokers\.


| Limit | Description | 
| --- | --- | 
| Broker name |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/amazon-mq-limits.html)  | 
| Brokers per AWS account, per region | 20 | 
| Broker configuration history depth | 10 | 
| Connections per wire\-level protocol | 1,000 \(100 for mq\.t2\.micro brokers\) | 
| Security groups per broker | 5 | 
| Destinations \(queues and topics\) monitored in CloudWatch | CloudWatch monitors only the first 200 destinations\. | 

## Configurations<a name="configuration-limits"></a>

The following table lists limits related to Amazon MQ configurations\.


| Limit | Description | 
| --- | --- | 
| Configuration name |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/amazon-mq-limits.html)  | 
| Configurations per AWS account | 1,000 | 
| Revisions per configuration | 300 | 

## Users<a name="activemq-user-limits"></a>

The following table lists limits related to Amazon MQ users\.


| Limit | Description | 
| --- | --- | 
| Username |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/amazon-mq-limits.html)  | 
| Password |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/amazon-mq-limits.html)  | 
| Users per broker | 250 | 
| Groups per user | 20 | 

## Data Storage<a name="data-storage-limits"></a>

The following table lists limits related to Amazon MQ data storage\.


| Limit | Description | 
| --- | --- | 
| Storage capacity per new mq\.t2\.micro broker\. See [ Instance Types  Lists the Amazon MQ broker instance types\.   The combined description of the broker instance *class* \(`m5`, `t2`\) and *size* \(`large`, `micro`\) is a *broker instance type* \(for example, `mq.m5.large`\)\. The following table lists the available Amazon MQ broker instance types\.  You can use Amazon EBS only with the `mq.m5` broker instance type family\. For more information, see [Storage](broker-storage.md)\.  

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/broker.html) For more information about throughput considerations, see [Choose the Correct Broker Instance Type for the Best Throughput](ensuring-effective-amazon-mq-performance.md#broker-instance-types-choosing)\. ](broker.md#broker-instance-types)\. | 20 GB | 
| Storage capacity per broker for other instance types\. See [ Instance Types  Lists the Amazon MQ broker instance types\.   The combined description of the broker instance *class* \(`m5`, `t2`\) and *size* \(`large`, `micro`\) is a *broker instance type* \(for example, `mq.m5.large`\)\. The following table lists the available Amazon MQ broker instance types\.  You can use Amazon EBS only with the `mq.m5` broker instance type family\. For more information, see [Storage](broker-storage.md)\.  

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/broker.html) For more information about throughput considerations, see [Choose the Correct Broker Instance Type for the Best Throughput](ensuring-effective-amazon-mq-performance.md#broker-instance-types-choosing)\. ](broker.md#broker-instance-types)\. | 200 GB | 

## API Throttling<a name="api-throttling-limits"></a>

The following throttling limits are aggregated per AWS account, *across all Amazon MQ APIs* to maintain service bandwidth\. For more information about Amazon MQ APIs, see the *[Amazon MQ REST API Reference](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/)*\.

**Important**  
These limits don't apply to ActiveMQ broker messaging APIs\. For example, Amazon MQ doesn't throttle the sending or receiving of messages\.


| Bucket Size | Refill Rate per Second | 
| --- | --- | 
| 100 | 15 | 