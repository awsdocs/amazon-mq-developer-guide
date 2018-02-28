# Amazon MQ Broker Architecture<a name="amazon-mq-broker-architecture"></a>

Amazon MQ brokers can be created as *single\-instance brokers* or *active/standby brokers for high availability*\. For both deployment modes, Amazon MQ provides high durability by storing its data redundantly, across multiple Availability Zones \(multi\-AZs\) within an AWS Region\. Amazon MQ ensures high availability by providing failover to a standby instance in a second Availability Zone\.

**Note**  
Amazon MQ uses [Apache KahaDB](http://activemq.apache.org/kahadb.html) as its data store\. Other data stores, such as JDBC and LevelDB, aren't supported\.


+ [Single\-Instance Broker](#single-broker-deployment)
+ [Active/Standby Broker for High Availability](#active-standby-broker-deployment)

## Single\-Instance Broker<a name="single-broker-deployment"></a>

A *single\-instance broker* is comprised of one *broker* in one Availability Zone\. The broker communicates with your application and with an AWS storage location\.

The following diagram illustrates a single\-instance broker\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-architecture-single-broker-deployment.png)

## Active/Standby Broker for High Availability<a name="active-standby-broker-deployment"></a>

An *active/standby broker for high availability* is comprised of two *brokers* in two different Availability Zones, configured in a *redundant pair*\. These brokers communicate with your application, and with a shared storage location\.

Normally, only one of the broker instances is active at any time, while the other broker instance is on standby\. If one of the broker instances malfunctions, it takes Amazon MQ a short while to take the malfunctioning instance out of service, allowing the healthy standby instance to become active and to begin accepting incoming communications\. When you reboot a broker, the failover takes only a few seconds\.

For an active/standby broker for high availability, Amazon MQ provides two ActiveMQ Web Console URLs, but only one URL is active at a time\. Likewise, Amazon MQ provides two endpoints for each wire\-level protocol, but only one endpoint is active in each pair at a time\. The `-1` and `-2` suffixes denote a redundant pair\. For wire\-level protocol endpoints, you can allow your application to connect to either endpoint by using the [Failover Transport](http://activemq.apache.org/failover-transport-reference.html)\.

The following diagram illustrates an active/standby broker for high availability\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-architecture-active-standby-deployment.png)