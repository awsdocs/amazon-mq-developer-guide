# Broker<a name="broker"></a>

A *broker* is a message broker environment running on Amazon MQ\. It is the basic building block of Amazon MQ\. The combined description of the broker instance *class* \(`m5`, `t3`\) and *size* \(`large`, `micro`\) is a *broker instance type* \(for example, `mq.m5.large`\)\. For more information, see [Instance types](broker-instance-types.md)\.
+ A *single\-instance broker* is comprised of one broker in one Availability Zone behind a Network Load Balancer \(NLB\)\. The broker communicates with your application and with Amazon EFS \(applicable only to ActiveMQ brokers\) or with Amazon EBS\.
+ An *active/standby broker* is comprised of two brokers in two different Availability Zones, configured in a *redundant pair*\. These brokers communicate synchronously with your application, and with Amazon EFS\.

For more information, see [Broker architecture](amazon-mq-broker-architecture.md)\.

You can enable *automatic minor version upgrades* to new minor versions of the broker engine, as Apache releases new versions\. Automatic upgrades occur during the *maintenance window* defined by the day of the week, the time of day \(in 24\-hour format\), and the time zone \(UTC by default\)\.

For information about creating and managing brokers, see the following:
+ [Creating and configuring an ActiveMQ broker](amazon-mq-creating-configuring-broker.md)
+ [Brokers](amazon-mq-limits.md#broker-limits)
+ [Broker statuses](broker-statuses.md)

## Supported wire\-level protocols<a name="broker-protocols"></a>

You can access your brokers by using [any programming language that ActiveMQ supports](http://activemq.apache.org/cross-language-clients.html) and by enabling TLS explicitly for the following protocols:
+ [AMQP](http://activemq.apache.org/amqp.html)
+ [MQTT](http://activemq.apache.org/mqtt.html)
+ MQTT over [WebSocket](http://activemq.apache.org/websockets.html)
+ [OpenWire](http://activemq.apache.org/openwire.html)
+ [STOMP](http://activemq.apache.org/stomp.html)
+ STOMP over WebSocket

## Attributes<a name="broker-attributes"></a>

An ActiveMQ broker has several attributes, for example:
+ A name \(`MyBroker`\)
+ An ID \(`b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`\)
+ An Amazon Resource Name \(ARN\) \(`arn:aws:mq:us-east-2:123456789012:broker:MyBroker:b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`\)
+ An ActiveMQ Web Console URL \(`https://b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.mq.us-east-2.amazonaws.com:8162`\)

  For more information, see [Web Console](http://activemq.apache.org/web-console.html) in the Apache ActiveMQ documentation\.
**Important**  
If you specify an authorization map which doesn't include the `activemq-webconsole` group, you can't use the ActiveMQ Web Console because the group isn't authorized to send messages to, or receive messages from, the Amazon MQ broker\.
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
For an active/standby broker, Amazon MQ provides two ActiveMQ Web Console URLs, but only one URL is active at a time\. Likewise, Amazon MQ provides two endpoints for each wire\-level protocol, but only one endpoint is active in each pair at a time\. The `-1` and `-2` suffixes denote a redundant pair\.

For a full list of broker attributes, see the following in the *Amazon MQ REST API Reference*:
+ [REST Operation ID: Broker](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker.html)
+ [REST Operation ID: Brokers](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-brokers.html)
+ [REST Operation ID: Broker Reboot](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker-reboot.html)