# Broker<a name="rabbitmq-basic-elements-broker"></a>

A *broker* is a message broker environment running on Amazon MQ\. It is the basic building block of Amazon MQ\. The combined description of the broker instance *class* \(`m5`, `t3`\) and *size* \(`large`, `micro`\) is a *broker instance type* \(for example, `mq.m5.large`\)\. For more information, see [Instance types](broker-instance-types.md)\.
+ A *single\-instance broker* is comprised of one broker in one Availability Zone\. The broker communicates with your application and with Amazon EBS\. Single\-instance brokers provide high throughput and lower latency\.
+ A *cluster deployment* is a logical grouping of three RabbitMQ broker nodes behind a Network Load Balancer \(NLB\), each sharing users, queues, and a distributed state across multiple Availability Zones \(AZ\)\.

For more information, see [Broker architecture](rabbitmq-broker-architecture.md)\.

You can enable *automatic minor version upgrades* to new minor versions of the broker engine, as new versions of the RabbitMQ engine are released\. Automatic upgrades occur during the *maintenance window* defined by the day of the week, the time of day \(in 24\-hour format\), and the time zone \(UTC by default\)\.

## Supported protocols<a name="broker-protocols"></a>

You can access your RabbitMQ brokers by using [any programming language that RabbitMQ supports](https://www.rabbitmq.com/devtools.html) and by enabling TLS for the following protocols:
+ [AMQP \(0\-9\-1\)](https://www.rabbitmq.com/specification.html)

## Listener ports<a name="rabbitmq-broker-listeners"></a>

 Amazon MQ managed RabbitMQ brokers support the following listener ports for application\-level connectivity via `amqps`, as well as client connections using the RabbitMQ web console and the management API\. 
+ Listener port `5671` \- Used for connections made via the secure AMQP URL\. For example, given a broker with broker ID `b-c8352341-ec91-4a78-ad9c-a43f23d325bb`, deployed in the `us-west-2` region, the following is the broker's full `amqp` URL: `b-c8352341-ec91-4a78-ad9c-a43f23d325bb.mq.us-west-2.amazonaws.com:5671`\.
+ Listener ports `443` and `15671` \- Both listener ports can be used interchangably to access a broker via the RabbitMQ web console or the mangement API\.

## Attributes<a name="broker-attributes"></a>

A RabbitMQ broker has several attributes:
+ A name\. For example, `MyBroker`\.
+ An ID\. For example, `b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`\.
+ An Amazon Resource Name \(ARN\)\. For example, `arn:aws:mq:us-east-2:123456789012:broker:MyBroker:b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`\.
+ A RabbitMQ web console URL\. For example, `https://b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.mq.us-east-2.amazonaws.com`\.

  For more information, see [RabbitMQ web console](https://www.rabbitmq.com/management.html) in the RabbitMQ documentation\.
+ A secure AMQP endpoint\. For example, `amqps://b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.mq.us-east-2.amazonaws.com`\.

For a full list of broker attributes, see the following in the *Amazon MQ REST API Reference*:
+ [REST Operation ID: Broker](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker.html)
+ [REST Operation ID: Brokers](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-brokers.html)
+ [REST Operation ID: Broker Reboot](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker-reboot.html)