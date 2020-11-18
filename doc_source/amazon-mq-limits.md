# Quotas in Amazon MQ<a name="amazon-mq-limits"></a>

This topic lists quotas within Amazon MQ\. Many of the following quotas can be changed for specific AWS accounts\. To request an increase for a limit, see [AWS Service Quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) in the *Amazon Web Services General Reference*\.

**Topics**
+ [Brokers](#broker-limits)
+ [Configurations](#configuration-limits)
+ [Users](#activemq-user-limits)
+ [Data Storage](#data-storage-limits)
+ [API Throttling](#api-throttling-limits)

## Brokers<a name="broker-limits"></a>

The following table lists quotas related to Amazon MQ brokers\.


| Limit | Description | 
| --- | --- | 
| Broker name |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/amazon-mq-limits.html)  | 
| Number of brokers, per region | 20 | 
| Wire\-level connections per smaller broker |   Does not apply to RabbitMQ brokers\.  100 for mq\.\*\.micro instance type brokers\.  | 
| Wire\-level connections per larger broker |   Does not apply to RabbitMQ brokers\.  1,000 for mq\.\*\.medium and mq\.\*\.\*large instance type brokers\.  | 
| Security groups per broker | 5 | 
| Destinations \(queues, ActiveMQ topics, and RabbitMQ exchanges\) monitored in CloudWatch | CloudWatch monitors only the first 200 destinations\. | 
| Tags per broker | 50 | 

## Configurations<a name="configuration-limits"></a>

The following table lists quotas related to Amazon MQ configurations\.

**Important**  
Does not apply to RabbitMQ brokers\.


| Limit | Description | 
| --- | --- | 
| Configuration name |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/amazon-mq-limits.html)  | 
| Revisions per configuration | 300 | 

## Users<a name="activemq-user-limits"></a>

The following table lists quotas related to Amazon MQ ActiveMQ broker users\.

**Important**  
Does not apply to RabbitMQ brokers\.


| Limit | Description | 
| --- | --- | 
| Username |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/amazon-mq-limits.html)  | 
| Password |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/amazon-mq-limits.html)  | 
| Users per broker \(simple auth\) | 250 | 
| Groups per user \(simple auth\) | 20 | 

## Data Storage<a name="data-storage-limits"></a>

The following table lists quotas related to Amazon MQ data storage\.


| Limit | Description | 
| --- | --- | 
| Storage capacity per smaller broker | 20 GB for mq\.\*\.micro instance type brokers\. For more information regarding Amazon MQ instance types, see [Instance types](broker-instance-types.md)\. | 
| Storage capacity per larger broker | 200 GB for mq\.\*\.medium and mq\.\*\.\*large instance type brokers\. For more information regarding Amazon MQ instance types, see [Instance types](broker-instance-types.md)\. | 
| Job scheduler usage limit per broker [backed by Amazon EBS](broker-storage.md) |   Does not apply to RabbitMQ brokers\.  50 GB\. For more information about job scheduler usage, see [JobSchedulerUsage](https://activemq.apache.org/maven/apidocs/org/apache/activemq/usage/JobSchedulerUsage.html) in the Apache ActiveMQ API Documentation\.  | 
| Temporary storage capacity per smaller broker\. |   Does not apply to RabbitMQ brokers\.  5 GB for mq\.\*\.micro instance type brokers\.  | 
| Temporary storage capacity per larger broker\. |   Does not apply to RabbitMQ brokers\.  50 GB for mq\.\*\.medium and mq\.\*\.\*large instance type brokers\.  | 

## API Throttling<a name="api-throttling-limits"></a>

The following throttling quotas are aggregated per AWS account, *across all Amazon MQ APIs* to maintain service bandwidth\. For more information about Amazon MQ APIs, see the *[Amazon MQ REST API Reference](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/)*\.

**Important**  
These quotas don't apply to ActiveMQ or RabbitMQ broker messaging APIs\. For example, Amazon MQ doesn't throttle the sending or receiving of messages\.


| API burst limit | API rate limit | 
| --- | --- | 
| 100 | 15 | 