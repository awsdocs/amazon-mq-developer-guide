# Single\-instance broker<a name="rabbitmq-broker-architecture-single-instance"></a>

A *single\-instance broker* is comprised of one broker in one Availability Zone\. The broker communicates with your application and with Amazon EBS\. Single\-instance brokers provide high throughput and lower latency\.\.

The following diagram illustrates a single\-instance broker with Amazon EBS storage replicated across multiple servers within a single Availability Zone \(AZ\)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-architecture-single-broker-deployment-ebs.png)