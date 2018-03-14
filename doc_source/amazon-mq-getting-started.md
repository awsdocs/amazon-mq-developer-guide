# Getting Started with Amazon MQ<a name="amazon-mq-getting-started"></a>

This section will help you become more familiar with Amazon MQ by showing you how to create a broker and how to connect your application to it\.


+ [Prerequisites](#create-broker-prerequisites)
+ [Create an ActiveMQ Broker](#create-activemq-broker)
+ [Connect a Java Application to Your Broker](#connect-java-application)
+ [Delete Your Broker](#delete-broker)
+ [Next Steps](#next-steps-tutorials)

## Prerequisites<a name="create-broker-prerequisites"></a>

Before you begin, complete the steps in [Setting Up Amazon MQ](amazon-mq-setting-up.md)\.

## Step 1: Create an ActiveMQ Broker<a name="create-activemq-broker"></a>

A *broker* is a message broker environment running on Amazon MQ\. It is the basic building block of Amazon MQ\. The combined description of the broker instance *class* \(`m4`, `t2`\) and *size* \(`large`, `micro`\) is a *broker instance type* \(for example, `mq.m4.large`\)\. For more information, see [Broker](amazon-mq-basic-elements.md#broker)\.

The first and most common Amazon MQ task is creating a broker\. The following example shows how you can use the AWS Management Console to create a basic broker\.

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. Do one of the following:

   + If this is your first time using Amazon MQ, in the **Create a broker** section, type `MyBroker` for **Broker name** and then choose **Next step**\.

   + If you have created a broker before, on the **Create a broker** page, in the **Broker details** section, type `MyBroker` for **Broker name**\.

1. Choose a **Broker instance type** \(for example, **mq\.m4\.large**\)\. For more information, see [Instance Types](amazon-mq-basic-elements.md#broker-instance-types)\.

1. For **Deployment mode**, ensure that **Single\-instance broker** is selected\.
**Note**  
Currently, Amazon MQ supports only the `ActiveMQ` broker engine, version `5.15.0`\.

1. In the **ActiveMQ Web Console access** section, type a **Username** and **Password**\.

1. Choose **Create broker**\.

   While Amazon MQ creates your broker, it displays the **Creation in progress** status\. 

   Creating the broker takes about 15 minutes\.

   When your broker is created successfully, Amazon MQ displays the **Running** status\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-getting-started-create-broker-running.png)

1. Choose ***MyBroker***\.

   On the ***MyBroker*** page, in the **Connect** section, note your broker's **[ActiveMQ Web Console](http://activemq.apache.org/web-console.html)** URL, for example:

   ```
   https://b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.mq.us-east-2.amazonaws.com:8162
   ```

   Also, note your broker's [wire\-level protocol **Endpoints**](http://activemq.apache.org/configuring-transports.html)\. The following is an example of an OpenWire endpoint:

   ```
   ssl://b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.mq.us-east-2.amazonaws.com:61617
   ```

## Step 2: Connect a Java Application to Your Broker<a name="connect-java-application"></a>

After you create an Amazon MQ broker, you can connect your application to it\. The following examples show how you can use the Java Message Service \(JMS\) to create a connection to the broker, create a queue, and send a message\. For a complete, working Java example, see [Working Example of Using Java Message Service \(JMS\) with ActiveMQ](amazon-mq-working-java-example.md)\.

You can connect to ActiveMQ brokers using [various ActiveMQ clients](http://activemq.apache.org/cross-language-clients.html)\. We recommend using the [ActiveMQ Client](https://mvnrepository.com/artifact/org.apache.activemq/activemq-client/5.15.0)\.

**Important**  
To ensure that your broker is accessible within your VPC, you must enable the `enableDnsHostnames` and `enableDnsSupport` VPC attributes\. For more information, see [DNS Support in your VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-dns.html#vpc-dns-support) in the *Amazon VPC User Guide*\.

### Prerequisites<a name="connect-application-prerequisites-getting-started"></a>

Add the `activemq-client.jar` and `activemq-pool.jar` packages to your Java build class path\. The following example shows these dependencies in a Maven project `pom.xml` file\.

```
<dependencies>
    <dependency>
        <groupId>org.apache.activemq</groupId>
        <artifactId>activemq-client</artifactId>
        <version>5.15.0</version>
    </dependency>
    <dependency>
        <groupId>org.apache.activemq</groupId>
        <artifactId>activemq-pool</artifactId>
        <version>5.15.0</version>
    </dependency>
</dependencies>
```

For more information about `activemq-client.jar`, see [Initial Configuration](http://activemq.apache.org/initial-configuration.html) in the Apache ActiveMQ documentation\.

### Create a Message Producer and Send a Message<a name="create-producer-send-message-getting-started"></a>

1. Create a JMS pooled connection factory for the message producer using your broker's endpoint and then call the `createConnection` method against the factory\.
**Note**  
For an active/standby broker for high availability, Amazon MQ provides two ActiveMQ Web Console URLs, but only one URL is active at a time\. Likewise, Amazon MQ provides two endpoints for each wire\-level protocol, but only one endpoint is active in each pair at a time\. The `-1` and `-2` suffixes denote a redundant pair\. For more information, see [Amazon MQ Broker Architecture](amazon-mq-broker-architecture.md)\)\.  
For wire\-level protocol endpoints, you can allow your application to connect to either endpoint by using the [Failover Transport](http://activemq.apache.org/failover-transport-reference.html)\.

   ```
   // Create a connection factory.
   final ActiveMQConnectionFactory connectionFactory = new ActiveMQConnectionFactory(wireLevelEndpoint);
   
   // Pass the username and password.
   connectionFactory.setUserName(activeMqUsername);
   connectionFactory.setPassword(activeMqPassword);
   
   // Create a pooled connection factory for the producer.
   final PooledConnectionFactory pooledConnectionFactoryProducer = new PooledConnectionFactory();
   pooledConnectionFactoryProducer.setConnectionFactory(connectionFactory);
   pooledConnectionFactoryProducer.setMaxConnections(10);
   
   // Establish a connection for the producer.
   final Connection producerConnection = pooledConnectionFactoryProducer.createConnection();
   producerConnection.start();
   ```
**Note**  
Always use the `PooledConnectionFactory` class\. For more information, see [Always Use Connection Pooling](communicating-with-amazon-mq.md#always-use-connection-pooling)\.

1. Create a session, a queue named `MyQueue`, and a message producer\.

   ```
   // Create a session.
   final Session producerSession = producerConnection.createSession(false, Session.AUTO_ACKNOWLEDGE);
   
   // Create a queue named "MyQueue".
   final Destination producerDestination = producerSession.createQueue("MyQueue");
   
   // Create a producer from the session to the queue.
   final MessageProducer producer = producerSession.createProducer(producerDestination);
   producer.setDeliveryMode(DeliveryMode.NON_PERSISTENT);
   ```

1. Create the message string `"Hello from Amazon MQ!"` and then send the message\.

   ```
   // Create a message.
   final String text = "Hello from Amazon MQ!";
   final TextMessage producerMessage = producerSession.createTextMessage(text);
   
   // Send the message.
   producer.send(producerMessage);
   System.out.println("Message sent.");
   ```

1. Clean up the producer\.

   ```
   producer.close();
   producerSession.close();
   producerConnection.close();
   ```

### Create a Message Consumer and Receive the Message<a name="create-consumer-receive-message-getting-started"></a>

1. Create a JMS pooled connection factory for the message consumer using your broker's endpoint and then call the `createConnection` method against the factory\.

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
**Note**  
Always use the `PooledConnectionFactory` class\. For more information, see [Always Use Connection Pooling](communicating-with-amazon-mq.md#always-use-connection-pooling)\.

1. Create a session, a queue named `MyQueue`, and a message consumer\.

   ```
   // Create a session.
   final Session consumerSession = consumerConnection.createSession(false, Session.AUTO_ACKNOWLEDGE);
   
   // Create a queue named "MyQueue".
   final Destination consumerDestination = consumerSession.createQueue("MyQueue");
   
   // Create a message consumer from the session to the queue.
   final MessageConsumer consumer = consumerSession.createConsumer(consumerDestination);
   ```

1. Begin to wait for messages and receive the message when it arrives\.

   ```
   // Begin to wait for messages.
   final Message consumerMessage = consumer.receive(1000);
   
   // Receive the message when it arrives.
   final TextMessage consumerTextMessage = (TextMessage) consumerMessage;
   System.out.println("Message received: " + consumerTextMessage.getText());
   ```
**Note**  
Unlike AWS messaging services \(such as Amazon SQS\), the consumer is constantly connected to the broker\.

1. Close the consumer, session, and connection\.

   ```
   consumer.close();
   consumerSession.close();
   consumerConnection.close();
   pooledConnectionFactoryConsumer.stop();
   ```

## Step 3: Delete Your Broker<a name="delete-broker"></a>

If you don't use an Amazon MQ broker \(and don't foresee using it in the near future\), it is a best practice to delete it from Amazon MQ to reduce your AWS costs\.

The following example shows how you can delete a broker using the AWS Management Console\.

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. From the broker list, select your broker \(for example, **MyBroker**\) and then choose **Delete**\.

1. In the **Delete *MyBroker*?** dialog box, type `delete` and then choose **Delete**\.

   Deleting a broker takes about 5 minutes\.

## Next Steps<a name="next-steps-tutorials"></a>

Now that you have created a broker, connected an application to it, and sent and received a message, you might want to try the following:

+ [Tutorial: Creating and Configuring an Amazon MQ Broker](amazon-mq-creating-configuring-broker.md) \(Advanced Settings\)

+ [Tutorial: Creating and Applying Amazon MQ Broker Configurations](amazon-mq-creating-applying-configurations.md)

+ [Tutorial: Editing Amazon MQ Broker Configurations and Managing Configuration Revisions](amazon-mq-editing-managing-configurations.md)

+ [Tutorial: Listing Amazon MQ Brokers and Viewing Broker Details](amazon-mq-listing-brokers.md)

+ [Tutorial: Creating and Managing Amazon MQ Broker Users](amazon-mq-listing-managing-users.md)

+ [Tutorial: Rebooting an Amazon MQ Broker](amazon-mq-rebooting-broker.md)

+ [Tutorial: Accessing CloudWatch Metrics for Amazon MQ](amazon-mq-accessing-metrics.md)

You can also begin to dive deep into [Amazon MQ best practices](amazon-mq-best-practices.md) and [Amazon MQ REST APIs](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/), and then [plan to migrate to Amazon MQ](amazon-mq-migrating.md)\.