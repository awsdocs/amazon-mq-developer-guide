# Amazon MQ single\-instance broker<a name="single-broker-deployment"></a>

A *single\-instance broker* is comprised of one broker in one Availability Zone behind a Network Load Balancer \(NLB\)\. The broker communicates with your application and with Amazon EFS \(applicable only to ActiveMQ brokers\) or with Amazon EBS\. For more information, see [Storage](broker-storage.md)\.

The following diagram illustrates a single\-instance broker with Amazon EFS storage replicated across multiple Availability Zones \(AZs\)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-architecture-single-broker-deployment-efs.png)

The following diagram illustrates a single\-instance broker with Amazon EBS storage replicated across multiple servers within a single AZ\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-architecture-single-broker-deployment-ebs.png)