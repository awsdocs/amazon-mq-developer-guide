# Storage<a name="broker-storage"></a>

By default, Amazon MQ uses Amazon Elastic File System \(Amazon EFS\) for broker storage\. To take advantage of high durability and replication across multiple Availability Zones, use Amazon EFS\. To take advantage of low latency and high throughput, use Amazon Elastic Block Store \(EBS\)\. For more information, see [Choose the Correct Broker Storage Type for the Best Throughput](ensuring-effective-amazon-mq-performance.md#broker-storage-types-choosing)\.

**Important**  
You can use EBS only with the `mq.m5` broker instance type family\.
Although you can change the *broker instance type*, you can't change the *broker storage type* after you create the broker\.
Amazon EBS within a single Availability Zone doesn't support [active/standby brokers](active-standby-broker-deployment.md)\.
We don't recommend using EBS as the sole message storage location for your broker\.

## Differences between Storage Types<a name="differences-between-storage-types"></a>

The following table provides a brief overview of the differences between in\-memory, Amazon EFS, and EBS storage types\.\.


| Storage Type | Persistence | Example Use Case | Approximate Maximum Number of Messages Enqueued per Producer, per Second \(1KB Message\) | Replication | 
| --- | --- | --- | --- | --- | 
| In\-Memory | Non\-persistent |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/broker-storage.html)  | 5,000 | None | 
| EBS | Persistent |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/broker-storage.html)  | 500 | Multiple copies within a single Availability Zone | 
| Amazon EFS | Persistent | Financial transactions | 80 | Multiple copies across multiple Availability Zones | 

In\-memory message storage provides the lowest latency and the highest throughput\. However, messages might be lost during instance replacement or broker restart\.

Amazon EFS is designed to be highly durable, replicated across multiple Availability Zones \(AZs\) to prevent the loss of data resulting from the failure of any single component or an issue that affects the availability of an AZ\. EBS is optimized for throughput and replicated across multiple servers within a single AZ\.

While EBS volumes are designed to provide high performance and low latency with high durability, Amazon EFS is designed to provide higher durability than EBS\.