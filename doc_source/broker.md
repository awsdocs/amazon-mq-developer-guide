# Broker<a name="broker"></a>

A *broker* is a message broker environment running on Amazon MQ\. It is the basic building block of Amazon MQ\. The combined description of the broker instance *class* \(`m4`, `t2`\) and *size* \(`large`, `micro`\) is a *broker instance type* \(for example, `mq.m4.large`\)\. For more information, see [Instance Types](#broker-instance-types)\.

+ A *single\-instance broker* is comprised of one broker in one Availability Zone\. The broker communicates with your application and with an AWS storage location\.

+ An *active/standby broker for high availability* is comprised of two brokers in two different Availability Zones, configured in a *redundant pair*\. These brokers communicate synchronously with your application, and with a shared storage location\.

For more information, see [Amazon MQ Broker Architecture](amazon-mq-broker-architecture.md)\.

You can enable *automatic minor version upgrades* to new minor versions of the broker engine, as Apache releases new versions\. Automatic upgrades occur during the *maintenance window*, defined by the day of the week, the time of day \(in 24\-hour format\), and the time zone \(UTC by default\)\.

For information about creating and managing brokers, see the following:

+ [Tutorial: Creating and Configuring an Amazon MQ Broker](amazon-mq-creating-configuring-broker.md)

+ [Brokers](amazon-mq-limits.md#broker-limits)

+ [Statuses](#broker-statuses)

## Attributes<a name="broker-attributes"></a>

A broker has several attributes, for example:

+ A name \(`MyBroker`\)

+ An ID \(`b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`\)

+ An Amazon Resource Name \(ARN\) \(`arn:aws:mq:us-east-2:123456789012:broker:MyBroker:b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`\)

+ An ActiveMQ Web Console URL \(`https://b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.mq.us-east-2.amazonaws.com:8162`\)

  For more information, see [Web Console](http://activemq.apache.org/web-console.html) in the Apache ActiveMQ documentation\.
**Important**  
If you specify an authorization map which doesn't include the `activemq-webconsole` group, you won't be able to use the ActiveMQ Web Console because the group isn't authorized to send messages to, or receive messages from, the Amazon MQ broker\.

+ Wire\-level protocol endpoints:

  + `amqp+ssl://b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.mq.us-east-2.amazonaws.com:5671`

  + `mqtt+ssl://b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.mq.us-east-2.amazonaws.com:8883`

  + `ssl://b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.mq.us-east-2.amazonaws.com:61617`
**Note**  
This is an OpenWire endpoint\.

  + `stomp+ssl://b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.mq.us-east-2.amazonaws.com:61614`

  + `wss://b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.mq.us-east-2.amazonaws.com:61619`

  For more information, see [Configuring Transports](http://activemq.apache.org/configuring-transports.html) in the Apache ActiveMQ documentation\.

**Note**  
For an active/standby broker for high availability, Amazon MQ provides two ActiveMQ Web Console URLs, but only one URL is active at a time\. Likewise, Amazon MQ provides two endpoints for each wire\-level protocol, but only one endpoint is active in each pair at a time\. The `-1` and `-2` suffixes denote a redundant pair\.

For a full list of broker attributes, see the following in the *Amazon MQ REST API Reference*:

+ [REST Operation ID: Broker](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker.html)

+ [REST Operation ID: Brokers](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-brokers.html)

+ [REST Operation ID: Broker Reboot](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker-reboot.html)

## Instance Types<a name="broker-instance-types"></a>

The combined description of the broker instance *class* \(`m4`, `t2`\) and *size* \(`large`, `micro`\) is a *broker instance type* \(for example, `mq.m4.large`\)\. The following table lists the available Amazon MQ broker instance types\.


| Instance Type | vCPU | Memory \(GiB\) | Network Performance | 
| --- | --- | --- | --- | 
| Standard | 
| mq\.m4\.large | 2 | 8 | Moderate | 
| Micro\-Instance | 
| mq\.t2\.micro | 1 | 1 | Low | 

**Note**  
The mq\.t2\.micro instance type \(single\-instance brokers only\) qualifies for the [AWS Free Tier](https://aws.amazon.com/free/)\.  
Using the `mq.t2.micro` instance type is subject to * [CPU credits and baseline performance](http://docs.aws.amazon.com/AWSEC2/latest/DeveloperGuide/t2-credits-baseline-concepts.html)*—with the ability to *burst* above the baseline level\. If your application requires *fixed performance*, consider using an `mq.m4.large` instance type\.

## Statuses<a name="broker-statuses"></a>

A broker's current condition is indicated by a *status*\. The following table lists the statuses of an Amazon MQ broker\.


| Console | API | Description | 
| --- | --- | --- | 
| Creation failed | CREATION\_FAILED | The broker couldn't be created\. | 
| Creation in progress | CREATION\_IN\_PROGRESS | The broker is currently being created\. | 
| Deletion in progress | DELETION\_IN\_PROGRESS | The broker is currently being deleted\. | 
| Reboot in progress | REBOOT\_IN\_PROGRESS | The broker is currently being rebooted\. | 
| Running | RUNNING | The broker is operational\. | 