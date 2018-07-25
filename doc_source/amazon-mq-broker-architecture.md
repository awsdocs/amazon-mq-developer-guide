# Amazon MQ Broker Architecture<a name="amazon-mq-broker-architecture"></a>

Amazon MQ brokers can be created as *single\-instance brokers* or *active/standby brokers for high availability*\. For both deployment modes, Amazon MQ provides high durability by storing its data redundantly, across multiple Availability Zones \(multi\-AZs\) within an AWS Region\. Amazon MQ ensures high availability by providing failover to a standby instance in a second Availability Zone\.

**Note**  
Amazon MQ uses [Apache KahaDB](http://activemq.apache.org/kahadb.html) as its data store\. Other data stores, such as JDBC and LevelDB, aren't supported\.

**Topics**
+ [Amazon MQ Single\-Instance Broker](single-broker-deployment.md)
+ [Amazon MQ Active/Standby Broker for High Availability](active-standby-broker-deployment.md)
+ [Amazon MQ Broker Configuration Lifecycle](amazon-mq-broker-configuration-lifecycle.md)