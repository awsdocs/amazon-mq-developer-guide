# Storage<a name="broker-storage"></a>

Amazon MQ for ActiveMQ supports Amazon Elastic File System \(EFS\) and Amazon Elastic Block Store \(EBS\)\. By default, ActiveMQ brokers use Amazon EFS for broker storage\. To take advantage of high durability and replication across multiple Availability Zones, use Amazon EFS\. To take advantage of low latency and high throughput, use Amazon EBS\.

**Important**  
You can use Amazon EBS only with the `mq.m5` broker instance type family\.
Although you can change the *broker instance type*, you can't change the *broker storage type* after you create the broker\.
Amazon EBS replicates data within a single Availability Zone and doesn't support the [ActiveMQ active/standby](active-standby-broker-deployment.md) deployment mode\.
Storage capacity is determined by *broker instance type* as noted in [Quotas](https://docs.aws.amazon.com/amazon-mq/latest/developer-guide/amazon-mq-limits.html#data-storage-limits)\.

## Differences between Storage Types<a name="differences-between-storage-types"></a>

The following table provides a brief overview of the differences between in\-memory, Amazon EFS, and Amazon EBS storage types for ActiveMQ brokers\.


| Storage Type | Persistence | Example Use Case | Approximate Maximum Number of Messages Enqueued per Producer, per Second \(1KB Message\) | Replication | 
| --- | --- | --- | --- | --- | 
| In\-memory | Non\-persistent |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/broker-storage.html)  | 5,000 | None | 
| Amazon EBS | Persistent |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/broker-storage.html)  | 500 | Multiple copies within a single Availability Zone \(AZ\) | 
| Amazon EFS | Persistent | Financial transactions | 80 | Multiple copies across multiple AZs | 

In\-memory message storage provides the lowest latency and the highest throughput\. However, messages are lost during instance replacement or broker restart\.

Amazon EFS is designed to be highly durable, replicated across multiple AZs to prevent the loss of data resulting from the failure of any single component or an issue that affects the availability of an AZ\. Amazon EBS is optimized for throughput and replicated across multiple servers within a single AZ\.
