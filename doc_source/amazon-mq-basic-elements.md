# Amazon MQ Basic Elements<a name="amazon-mq-basic-elements"></a>

This section introduces key concepts essential to understanding Amazon MQ\.

## Broker<a name="broker"></a>

A *broker* is a message broker environment running on Amazon MQ\. It is the basic building block of Amazon MQ\. The combined description of the broker instance *class* \(`m4`, `t2`\) and *size* \(`large`, `micro`\) is a *broker instance type* \(for example, `mq.m4.large`\)\. For more information, see [Instance Types](#broker-instance-types)\.

+ A *single\-instance broker* is comprised of one *broker* in one Availability Zone\. The broker communicates with your application and with an AWS storage location\.

+ An *active/standby broker for high availability* is comprised of two *brokers* in two different Availability Zones, configured in a *redundant pair*\. These brokers communicate with your application, and with a shared storage location\.

For more information, see [Amazon MQ Broker Architecture](amazon-mq-broker-architecture.md)\.

You can enable *automatic minor version upgrades* to new minor versions of the broker engine, as Apache releases new versions\. Automatic upgrades occur during the *maintenance window*, defined by the day of the week, the time of day \(in 24\-hour format\), and the time zone \(UTC by default\)\.

For information about creating and managing brokers, see the following:

+ [Tutorial: Creating and Configuring an Amazon MQ Broker](amazon-mq-creating-configuring-broker.md)

+ [Brokers](amazon-mq-limits.md#broker-limits)

+ [Statuses](#broker-statuses)

### Attributes<a name="broker-attributes"></a>

A broker has several attributes, for example:

+ A name \(`MyBroker`\)

+ An ID \(`b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`\)

+ An Amazon Resource Name \(ARN\) \(`arn:aws:mq:us-east-2:123456789012:broker:MyBroker:b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`\)

+ An ActiveMQ Web Console URL \(`https://b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.mq.us-east-2.amazonaws.com:8162`\)

  For more information, see [Web Console](http://activemq.apache.org/web-console.html) in the Apache ActiveMQ documentation\.

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

### Instance Types<a name="broker-instance-types"></a>

The combined description of the broker instance *class* \(`m4`, `t2`\) and *size* \(`large`, `micro`\) is a *broker instance type* \(for example, `mq.m4.large`\)\. The following table lists the available Amazon MQ broker instance types\.


| Instance Type | vCPU | Memory \(GiB\) | Network Performance | 
| --- | --- | --- | --- | 
| Standard | 
| mq\.m4\.large | 2 | 8 | Moderate | 
| Micro\-Instance | 
| mq\.t2\.micro | 1 | 1 | Low | 

**Note**  
The mq\.t2\.micro instance type \(single\-instance brokers only\) qualifies for the [AWS Free Tier](https://aws.amazon.com/free/)\.

### Statuses<a name="broker-statuses"></a>

A broker's current condition is indicated by a *status*\. The following table lists the statuses of an Amazon MQ broker\.


| Console | API | Description | 
| --- | --- | --- | 
| Creation failed | CREATION\_FAILED | The broker couldn't be created\. | 
| Creation in progress | CREATION\_IN\_PROGRESS | The broker is currently being created\. | 
| Deletion in progress | DELETION\_IN\_PROGRESS | The broker is currently being deleted\. | 
| Reboot in progress | REBOOT\_IN\_PROGRESS | The broker is currently being rebooted\. | 
| Running | RUNNING | The broker is operational\. | 

## Configuration<a name="configuration"></a>

A *configuration* contains all of the settings for your ActiveMQ broker, in XML format \(similar to ActiveMQ's `activemq.xml` file\)\. You can create a configuration before creating any brokers\. You can then apply the configuration to one or more brokers\.

**Important**  
Making changes to a configuration does *not* apply the changes to the broker immediately\. To apply your changes, you must wait for the next maintenance window or reboot the broker\. For more information, see [Amazon MQ Broker Configuration Lifecycle](amazon-mq-broker-configuration-lifecycle.md)\.  
Currently, it isn't possible to delete a configuration\.

For information about creating, editing, and managing configurations, see the following:

+ [Tutorial: Creating and Applying Amazon MQ Broker Configurations](amazon-mq-creating-applying-configurations.md)

+ [Tutorial: Editing Amazon MQ Broker Configurations and Managing Configuration Revisions](amazon-mq-editing-managing-configurations.md)

+ [Configurations](amazon-mq-limits.md#configuration-limits)

+ [Amazon MQ Broker Configuration Parameters](amazon-mq-broker-configuration-parameters.md)

To keep track of the changes you make to your configuration, you can create *configuration revisions*\. For more information, see [Tutorial: Creating and Applying Amazon MQ Broker Configurations](amazon-mq-creating-applying-configurations.md) and [Tutorial: Editing Amazon MQ Broker Configurations and Managing Configuration Revisions](amazon-mq-editing-managing-configurations.md)\.

### Attributes<a name="configuration-attributes"></a>

A broker configuration has several attributes, for example:

+ A name \(`MyConfiguration`\)

+ An ID \(`c-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`\)

+ An Amazon Resource Name \(ARN\) \(`arn:aws:mq:us-east-2:123456789012:configuration:MyConfiguration:c-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`\)

For a full list of configuration attributes, see the following in the *Amazon MQ REST API Reference*:

+ [REST Operation ID: Configuration](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration.html)

+ [REST Operation ID: Configurations](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configurations.html)

For a full list of configuration revision attributes, see the following:

+ [REST Operation ID: Configuration Revision](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration-revision.html)

+ [REST Operation ID: Configuration Revisions](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration-revisions.html)

## Engine<a name="broker-engine"></a>

A *broker engine* is a type of message broker that runs on Amazon MQ\. 

**Note**  
Currently, Amazon MQ supports only the `ActiveMQ` broker engine, version `5.15.0`\.

## User<a name="user"></a>

An ActiveMQ *user* is a person or an application that can access the queues and topics of an ActiveMQ broker\. You can configure users to have specific permissions\. For example, you can allow some users to access the [ActiveMQ Web Console](http://activemq.apache.org/web-console.html)\.

A user can belong to a *group*\. You can configure which users belong to which groups and which groups have permission to send to, receive from, and administer specific queues and topics\.

**Important**  
Making changes to a user does *not* apply the changes to the user immediately\. To apply your changes, you must wait for the next maintenance window or reboot the broker\. For more information, see [Amazon MQ Broker Configuration Lifecycle](amazon-mq-broker-configuration-lifecycle.md)\.

For information about users and groups, see the following in the Apache ActiveMQ documentation:

+ [Authorization](http://activemq.apache.org/security.html#Security-Authorization)

+ [Authorization Example](http://activemq.apache.org/security.html#Security-AuthorizationExample)

For information about creating, editing, and deleting ActiveMQ users, see the following:

+ [Tutorial: Creating and Managing Amazon MQ Broker Users](amazon-mq-listing-managing-users.md)

+ [Users](amazon-mq-limits.md#activemq-user-limits)

### Attributes<a name="user-attributes"></a>

For a full list of user attributes, see the following in the *Amazon MQ REST API Reference*:

+ [REST Operation ID: User](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-user.html)

+ [REST Operation ID: Users](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-users.html)