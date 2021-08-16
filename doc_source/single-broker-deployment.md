# Amazon MQ single\-instance broker<a name="single-broker-deployment"></a>

A *single\-instance broker* is comprised of one broker in one Availability Zone\. The broker communicates with your application and with an Amazon EBS or Amazon EFS storage volume\. Amazon EFS storage volumes are designed to provide the highest level of durability and availability by storing data redundantly across multiple Availability Zones \(AZs\)\. Amazon EBS provides block level storage optimized for low\-latency and high throughput\. For more information about storage options, see [Storage](broker-storage.md)\.

The following diagram illustrates a single\-instance broker with Amazon EFS storage replicated across multiple AZs\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-activemq-broker-architecture-single-broker-efs.png)

The following diagram illustrates a single\-instance broker with Amazon EBS storage replicated across multiple servers within a single AZ\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-activemq-broker-architecture-single-broker-ebs.png)