# Single\-instance broker<a name="rabbitmq-broker-architecture-single-instance"></a>

A *single\-instance broker* is comprised of one broker in one Availability Zone behind a Network Load Balancer \(NLB\)\. The broker communicates with your application and with Amazon Elastic Block Store \(EBS\)\. Single\-instance brokers provide high throughput and lower latency\.

 Using an Network Load Balancer ensures that your Amazon MQ for RabbitMQ broker endpoint remains unchanged if the broker instance is replaced during a maintenance window or because of underlying Amazon EC2 hardware failures\. An Network Load Balancer allows your applications and users to continue to use the same endpoint to connect to the broker\.

The following diagram illustrates an Amazon MQ for RabbitMQ single\-instance broker\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-rabbitmq-broker-architecture-single-broker.png)