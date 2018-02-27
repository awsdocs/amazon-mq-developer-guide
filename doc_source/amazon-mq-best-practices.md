# Best Practices for Amazon MQ<a name="amazon-mq-best-practices"></a>

Use these best practices to make the most of Amazon MQ\.


+ [Using Amazon MQ Securely](#using-amazon-mq-securely)
+ [Communicating with Amazon MQ](#communicating-with-amazon-mq)

## Using Amazon MQ Securely<a name="using-amazon-mq-securely"></a>

### Always Create Brokers inside a Virtual Private Cloud \(VPC\)<a name="always-create-brokers-in-vpc"></a>

Brokers created without public accessibility can't be accessed from outside of your [VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Introduction.html)\. This greatly reduces your broker's susceptibility to Distributed Denial of Service \(DDoS\) attacks from the public Internet\. For more information, see [How to Help Prepare for DDoS Attacks by Reducing Your Attack Surface](http://aws.amazon.com/blogs/security/how-to-help-prepare-for-ddos-attacks-by-reducing-your-attack-surface/) on the AWS Security Blog\.

### Always Use Client\-Side Encryption as a Complement to TLS<a name="always-use-client-side-encryption-complement-tls"></a>

You can access your brokers using the following protocols with TLS enabled:

+ [AMQP](http://activemq.apache.org/amqp.html)

+ [MQTT](http://activemq.apache.org/mqtt.html)

+ MQTT over [WebSocket](http://activemq.apache.org/websockets.html)

+ [OpenWire](http://activemq.apache.org/openwire.html)

+ [STOMP](http://activemq.apache.org/stomp.html)

+ STOMP over WebSocket

Amazon MQ provides at\-rest encryption using an AWS\-managed Customer Master Key \(CMK\)\. For additional security, we highly recommend to design your application to use client\-side encryption\. For more information, see the *[AWS Encryption SDK Developer Guide](http://docs.aws.amazon.com/encryption-sdk/latest/developer-guide/)*\.

## Communicating with Amazon MQ<a name="communicating-with-amazon-mq"></a>

The following design patterns can improve the effectiveness of your application's communication with Amazon MQ brokers\.

### Never Modify or Delete the Amazon MQ Elastic Network Interface<a name="never-modify-delete-elastic-network-interface"></a>

When you first create an Amazon MQ broker, Amazon MQ provisions an [elastic network interface](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_ElasticNetworkInterfaces.html) in the [Virtual Private Cloud \(VPC\)](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Introduction.html) under your account and, thus, requires a number of EC2 permissions\. The network interface allows your client \(producer or consumer\) to communicate with the Amazon MQ broker\. The network interface is considered to be within the *service scope* of Amazon MQ, despite being part of your account's VPC\.

**Warning**  
You must not modify or delete this network interface\. Modifying or deleting the network interface can cause a permanent loss of connection between your VPC and your broker\.  
**Currently, you can't recover your broker if you delete its network interface\.** You can only recreate your broker\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-network-configuration-architecture-vpc-elastic-network-interface.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/)

### Always Use Connection Pooling<a name="always-use-connection-pooling"></a>

In a scenario with a single producer and single consumer \(such as the [Getting Started with Amazon MQ](amazon-mq-getting-started.md) tutorial\), you can use a single [http://activemq.apache.org/maven/apidocs/org/apache/activemq/ActiveMQConnectionFactory.html](http://activemq.apache.org/maven/apidocs/org/apache/activemq/ActiveMQConnectionFactory.html) class for every producer and consumer\. For example:

```
// Create a connection factory.
final ActiveMQConnectionFactory connectionFactory = new ActiveMQConnectionFactory(wireLevelEndpoint);

// Pass the username and password.
connectionFactory.setUserName(activeMqUsername);
connectionFactory.setPassword(activeMqPassword);

// Establish a connection for the consumer.
final Connection consumerConnection = connectionFactory.createConnection();
consumerConnection.start();
```

However, in more realistic scenarios with multiple producers and consumers, it can be costly and inefficient to create a large number of connections for multiple producers or consumers\. In these scenarios, you should group multiple producer or consumer requests using the [http://activemq.apache.org/maven/apidocs/org/apache/activemq/jms/pool/PooledConnectionFactory.html](http://activemq.apache.org/maven/apidocs/org/apache/activemq/jms/pool/PooledConnectionFactory.html) class for better throughput\. For example:

```
// Create a connection factory.
final ActiveMQConnectionFactory connectionFactory = new ActiveMQConnectionFactory(wireLevelEndpoint);

// Pass the username and password.
connectionFactory.setUserName(activeMqUsername);
connectionFactory.setPassword(activeMqPassword);

// Create a pooled connection factory for the consumer.
final PooledConnectionFactory pooledConnectionFactoryConsumer = new PooledConnectionFactory();
pooledConnectionFactoryConsumer.setConnectionFactory(connectionFactory);
pooledConnectionFactoryConsumer.setMaxConnections(10);

// Establish a connection for the consumer.
final Connection consumerConnection = pooledConnectionFactoryProducer.createConnection();
consumerConnection.start();
```

### Use the Failover Transport to Connect to Multiple Broker Endpoints<a name="use-failover-transport-connect-to-multiple-broker-endpoints"></a>

If you need your application to connect to multiple broker endpoints—for example, when you use an active/standby broker for high availability or when you [migrate from an on\-premises message broker to Amazon MQ]()—use the [Failover Transport](http://activemq.apache.org/failover-transport-reference.html) to allow your consumers to randomly connect to either one\. For example:

```
failover:(ssl://b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.mq.us-east-2.amazonaws.com:61617,ssl://b-9876l5k4-32ji-109h-8gfe-7d65c4b132a1-2.mq.us-east-2.amazonaws.com:61617)?randomize=true
```

### Avoid Using Message Selectors<a name="avoid-using-message-selectors"></a>

It is possible to use [JMS selectors](https://docs.oracle.com/cd/E19798-01/821-1841/bncer/index.html) to attach filters to topic subscriptions \(to route messages to consumers based on their content\)\. However, the use of JMS selectors fills up the Amazon MQ broker's filter buffer, preventing it from filtering messages\.

In general, avoid letting consumers route messages because, for optimal decoupling of consumers and producers, both the consumer and the producer should be ephemeral\.

### Prefer Virtual Destinations over Durable Subscriptions<a name="avoid-using-durable-subscriptions"></a>

A [durable subscription](http://activemq.apache.org/how-do-durable-queues-and-topics-work.html) can help ensure that the consumer receives all messages published to a topic, for example, after a lost connection is restored\. However, the use of durable subscriptions also precludes the use of competing consumers and might have performance issues at scale\. Consider using [virtual destinations](http://activemq.apache.org/virtual-destinations.html) instead\.