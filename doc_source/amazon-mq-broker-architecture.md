# Amazon MQ Broker Architecture<a name="amazon-mq-broker-architecture"></a>

Amazon MQ brokers can be created as *single\-instance brokers* or *active/standby brokers for high availability*\. For both deployment modes, Amazon MQ provides high durability by storing its data redundantly, across multiple Availability Zones \(multi\-AZs\) within an AWS Region\. Amazon MQ ensures high availability by providing failover to a standby instance in a second Availability Zone\.

**Note**  
Amazon MQ uses [Apache KahaDB](http://activemq.apache.org/kahadb.html) as its data store\. Other data stores, such as JDBC and LevelDB, aren't supported\.


+ [Single\-Instance Broker](single-broker-deployment.md)
+ [Active/Standby Broker for High Availability](active-standby-broker-deployment.md)
+ [Concurrent Store and Dispatch for Queues](concurrent-store-and-dispatch-for-queues.md)