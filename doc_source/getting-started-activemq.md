# Creating and connecting to an ActiveMQ broker<a name="getting-started-activemq"></a>

A *broker* is a message broker environment running on Amazon MQ\. It is the basic building block of Amazon MQ\. The combined description of the broker instance *class* \(`m5`, `t3`\) and *size* \(`large`, `micro`\) is a *broker instance type* \(for example, `mq.m5.large`\)\. For more information, see [Broker](broker.md)\.

**Topics**
+ [Create an ActiveMQ broker](#create-activemq-broker)
+ [Connect a Java application to your broker](#connect-java-application)
+ [Connect your Amazon MQ for ActiveMQ broker to Lambda](#activemq-connect-to-lambda)
+ [Delete your broker](#delete-broker)
+ [Next steps](#next-steps-tutorials)

## Step 1: create an ActiveMQ broker<a name="create-activemq-broker"></a>

The first and most common Amazon MQ task is creating a broker\. The following example shows how you can use the AWS Management Console to create a basic broker\.

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. On the **Select broker engine** page, choose **Apache ActiveMQ**\.

1. On the **Select deployment and storage** page, in the **Deployment mode and storage type** section, do the following:

   1. Choose the **Deployment mode** \(for example, **Active/standby broker**\)\. For more information, see [Broker architecture](amazon-mq-broker-architecture.md)\.
      + A **Single\-instance broker** has one broker in one Availability Zone\. The broker communicates with your application and with an Amazon EBS or Amazon EFS storage volume\. For more information, see [Amazon MQ single\-instance broker](single-broker-deployment.md)\.
      + An **Active/standby broker for high availability** is comprised of two brokers in two different Availability Zones, configured in a *redundant pair*\. These brokers communicate synchronously with your application, and with Amazon EFS\. For more information, see [Amazon MQ active/standby broker for high availability](active-standby-broker-deployment.md)\.
      + For more information on the sample blueprints for a network of brokers, see [Sample blueprints](network-of-brokers.md#sample-deployments)\.

   1. Choose the **Storage type** \(for example, **EBS**\)\. For more information, see [Storage](broker-storage.md)\.
**Note**  
Amazon EBS replicates data within a single Availability Zone and doesn't support the [ActiveMQ active/standby](active-standby-broker-deployment.md) deployment mode\.

   1. Choose **Next**\.

1. On the **Configure settings** page, in the **Details** section, do the following:

   1. Enter the **Broker name**\.
**Important**  
 We recommend not using any personally identifiable information in broker names\. 

   1. Choose the **Broker instance type** \(for example, **mq\.m5\.large**\)\. For more information, see [Instance types](broker-instance-types.md)\.

1. In the **ActiveMQ Web Console access** section, provide a **Username** and **Password**\.
**Important**  
Your username can contain only alphanumeric characters, dashes, periods, underscores, and tildas \(\- \. \_ \~\)\.
 Your password must be at least 12 characters long, contain at least 4 unique characters and must not contain commas, colons, or equal signs \(,:=\)\.

1. Choose **Deploy**\.

   While Amazon MQ creates your broker, it displays the **Creation in progress** status\. 

   Creating the broker takes about 15 minutes\.

   When your broker is created successfully, Amazon MQ displays the **Running** status\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-getting-started-create-broker-running.png)

1. Choose ***MyBroker***\.

   On the ***MyBroker*** page, in the **Connect** section, note your broker's **[ActiveMQ web console](http://activemq.apache.org/web-console.html)** URL, for example:

   ```
   https://b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.mq.us-east-2.amazonaws.com:8162
   ```

   Also, note your broker's [wire\-level protocol **Endpoints**](http://activemq.apache.org/configuring-transports.html)\. The following is an example of an OpenWire endpoint:

   ```
   ssl://b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.mq.us-east-2.amazonaws.com:61617
   ```

## Step 2: connect a Java application to your broker<a name="connect-java-application"></a>

After you create an Amazon MQ ActiveMQ broker, you can connect your application to it\. The following examples show how you can use the Java Message Service \(JMS\) to create a connection to the broker, create a queue, and send a message\. For a complete, working Java example, see [Working examples of using Java Message Service \(JMS\) with ActiveMQ](amazon-mq-working-java-example.md)\.

You can connect to ActiveMQ brokers using [various ActiveMQ clients](http://activemq.apache.org/cross-language-clients.html)\. We recommend using the [ActiveMQ Client](https://mvnrepository.com/artifact/org.apache.activemq/activemq-client)\.

### Prerequisites<a name="connect-application-prerequisites-getting-started"></a>

#### Enable VPC attributes<a name="connect-application-enable-vpc-attributes-getting-started"></a>

To ensure that your broker is accessible within your VPC, you must enable the `enableDnsHostnames` and `enableDnsSupport` VPC attributes\. For more information, see [DNS Support in your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-support) in the *Amazon VPC User Guide*\.

#### Enable inbound connections<a name="connect-application-allow-inbound-connections-getting-started"></a>

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. From the broker list, choose the name of your broker \(for example, **MyBroker**\)\.

1. On the ***MyBroker*** page, in the **Connections** section, note the addresses and ports of the broker's web console URL and wire\-level protocols\.

1. In the **Details** section, under **Security and network**, choose the name of your security group or ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-tutorials-broker-details-link.png)\.

   The **Security Groups** page of the EC2 Dashboard is displayed\.

1. From the security group list, choose your security group\.

1. At the bottom of the page, choose **Inbound**, and then choose **Edit**\.

1. In the **Edit inbound rules** dialog box, add a rule for every URL or endpoint that you want to be publicly accessible \(the following example shows how to do this for a broker web console\)\.

   1. Choose **Add Rule**\.

   1. For **Type**, select **Custom TCP**\.

   1. For **Port Range**, type the web console port \(`8162`\)\.

   1. For **Source**, leave **Custom** selected and then type the IP address of the system that you want to be able to access the web console \(for example, `192.0.2.1`\)\.

   1. Choose **Save**\.

      Your broker can now accept inbound connections\.

#### Add Java dependencies<a name="connect-application-java-dependencies-getting-started"></a>

Add the `activemq-client.jar` and `activemq-pool.jar` packages to your Java class path\. The following example shows these dependencies in a Maven project `pom.xml` file\.

```
<dependencies>
    <dependency>
        <groupId>org.apache.activemq</groupId>
        <artifactId>activemq-client</artifactId>
        <version>5.15.8</version>
    </dependency>
    <dependency>
        <groupId>org.apache.activemq</groupId>
        <artifactId>activemq-pool</artifactId>
        <version>5.15.8</version>
    </dependency>
</dependencies>
```

For more information about `activemq-client.jar`, see [Initial Configuration](http://activemq.apache.org/initial-configuration.html) in the Apache ActiveMQ documentation\.

**Important**  
In the following example code, producers and consumers run in a single thread\. For production systems \(or to test broker instance failover\), make sure that your producers and consumers run on separate hosts or threads\.

### Create a message producer and send a message<a name="create-producer-send-message-getting-started"></a>

1. Create a JMS pooled connection factory for the message producer using your broker's endpoint and then call the `createConnection` method against the factory\.
**Note**  
For an active/standby broker, Amazon MQ provides two ActiveMQ Web Console URLs, but only one URL is active at a time\. Likewise, Amazon MQ provides two endpoints for each wire\-level protocol, but only one endpoint is active in each pair at a time\. The `-1` and `-2` suffixes denote a redundant pair\. For more information, see [Broker architecture](amazon-mq-broker-architecture.md)\)\.  
For wire\-level protocol endpoints, you can allow your application to connect to either endpoint by using the [Failover Transport](http://activemq.apache.org/failover-transport-reference.html)\.

   ```
   // Create a connection factory.
   final ActiveMQConnectionFactory connectionFactory = new ActiveMQConnectionFactory(wireLevelEndpoint);
   
   // Pass the username and password.
   connectionFactory.setUserName(activeMqUsername);
   connectionFactory.setPassword(activeMqPassword);
   
   // Create a pooled connection factory.
   final PooledConnectionFactory pooledConnectionFactory = new PooledConnectionFactory();
   pooledConnectionFactory.setConnectionFactory(connectionFactory);
   pooledConnectionFactory.setMaxConnections(10);
   
   // Establish a connection for the producer.
   final Connection producerConnection = pooledConnectionFactory.createConnection();
   producerConnection.start();
   ```
**Note**  
Message producers should always use the `PooledConnectionFactory` class\. For more information, see [Always Use Connection Pooling](connecting-to-amazon-mq.md#always-use-connection-pooling)\.

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
   TextMessage producerMessage = producerSession.createTextMessage(text);
   
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

### Create a message consumer and receive the message<a name="create-consumer-receive-message-getting-started"></a>

1. Create a JMS connection factory for the message producer using your broker's endpoint and then call the `createConnection` method against the factory\.

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
**Note**  
Message consumers should *never* use the `PooledConnectionFactory` class\. For more information, see [Always Use Connection Pooling](connecting-to-amazon-mq.md#always-use-connection-pooling)\.

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
   ```

## Step 3: \(Optional\) connect to an AWS Lambda function<a name="activemq-connect-to-lambda"></a>

 AWS Lambda can connect to and consume messages from your Amazon MQ broker\. When you connect a broker to Lambda, you create an [event source mapping](https://docs.aws.amazon.com/lambda/latest/dg/invocation-eventsourcemapping.html) that reads messages from a queue and invokes the function [synchronously](https://docs.aws.amazon.com/lambda/latest/dg/invocation-sync.html)\. The event source mapping you create reads messages from your broker in batches and converts them into a Lambda payload in the form of a JSON object\. 

**To connect your broker to a Lambda function**

1. Add the following IAM role permissions to your Lambda function [execution role](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html)\.
   + [mq:DescribeBroker](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id.html#brokers-broker-id-http-methods)
   + [ec2:CreateNetworkInterface](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateNetworkInterface.html)
   + [ec2:DeleteNetworkInterface](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteNetworkInterface.html)
   + [ec2:DescribeNetworkInterfaces](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeNetworkInterfaces.html)
   + [ec2:DescribeSecurityGroups](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeSecurityGroups.html)
   + [ec2:DescribeSubnets](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeSubnets.html)
   + [ec2:DescribeVpcs](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeVpcs.html)
   + [logs:CreateLogGroup](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogGroup.html)
   + [logs:CreateLogStream](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogStream.html)
   + [logs:PutLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutLogEvents.html)
   + [secretsmanager:GetSecretValue](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html)
**Note**  
Without the necessary IAM permissions, your function will not be able to successfully read records from Amazon MQ resources\.

1.  \(Optional\) If you have created a broker without public accessibility, you must do one of the following to allow Lambda to connect to your broker: 
   +  Configure one NAT gateway per public subnet\. For more information, see [Internet and service access for VPC\-connected functions](https://docs.aws.amazon.com/lambda/latest/dg/configuration-vpc.html#vpc-internet) in the *AWS Lambda Developer Guide*\. 
   + Create a connection between your Amazon Virtual Private Cloud \(Amazon VPC\) and Lambda using a VPC endpoint\. Your Amazon VPC must also connect to AWS Security Token Service \(AWS STS\) and Secrets Manager endpoints\. For more information, see [Configuring interface VPC endpoints for Lambda](https://docs.aws.amazon.com/ambda/latest/dg/configuration-vpc-endpoints.html) in the *AWS Lambda Developer Guide*\. 

1.  [Configure your broker as an event source](https://docs.aws.amazon.com/lambda/latest/dg/with-mq.html#services-mq-eventsourcemapping) for a Lambda function using the AWS Management Console\. You can also use the [https://docs.aws.amazon.com/cli/latest/reference/lambda/create-event-source-mapping.html](https://docs.aws.amazon.com/cli/latest/reference/lambda/create-event-source-mapping.html) AWS Command Line Interface command\. 

1.  Write some code for your Lambda function to process the messages from your consumed from your broker\. The Lambda payload that retrieved by your event source mapping depends on the engine type of the broker\. The following is an example of a Lambda payload for an Amazon MQ for ActiveMQ queue\. 
**Note**  
 In the example, `testQueue` is the name of the queue\. 

   ```
   {
     "eventSource": "aws:amq",
     "eventSourceArn": "arn:aws:mq:us-west-2:112556298976:broker:test:b-9bcfa592-423a-4942-879d-eb284b418fc8",
     "messages": {
       [
         {
           "messageID": "ID:b-9bcfa592-423a-4942-879d-eb284b418fc8-1.mq.us-west-2.amazonaws.com-37557-1234520418293-4:1:1:1:1",
           "messageType": "jms/text-message",
           "data": "QUJDOkFBQUE=",
           "connectionId": "myJMSCoID",
           "redelivered": false,
           "destination": {
             "physicalname": "testQueue" 
           }, 
           "timestamp": 1598827811958,
           "brokerInTime": 1598827811958,
           "brokerOutTime": 1598827811959
         },
         {
           "messageID": "ID:b-9bcfa592-423a-4942-879d-eb284b418fc8-1.mq.us-west-2.amazonaws.com-37557-1234520418293-4:1:1:1:1",
           "messageType":"jms/bytes-message",
           "data": "3DTOOW7crj51prgVLQaGQ82S48k=",
           "connectionId": "myJMSCoID1",
           "persistent": false,
           "destination": {
             "physicalname": "testQueue" 
           }, 
           "timestamp": 1598827811958,
           "brokerInTime": 1598827811958,
           "brokerOutTime": 1598827811959
         }
       ]
     }
   }
   ```

For more information about connecting Amazon MQ to Lambda, the options Lambda supports for an Amazon MQ event source, and event source mapping errors, see [Using Lambda with Amazon MQ](https://docs.aws.amazon.com/lambda/latest/dg/with-mq.html) in the *AWS Lambda Developer Guide*\.

## Step 4: delete your broker<a name="delete-broker"></a>

If you don't use an Amazon MQ broker \(and don't foresee using it in the near future\), it is a best practice to delete it from Amazon MQ to reduce your AWS costs\.

The following example shows how you can delete a broker using the AWS Management Console\.

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. From the broker list, select your broker \(for example, **MyBroker**\) and then choose **Delete**\.

1. In the **Delete *MyBroker*?** dialog box, type `delete` and then choose **Delete**\.

   Deleting a broker takes about 5 minutes\.

## Next steps<a name="next-steps-tutorials"></a>

Now that you have created a broker, connected an application to it, and sent and received a message, you might want to try the following:
+ [Creating and configuring an ActiveMQ broker](amazon-mq-creating-configuring-broker.md) \(Additional Settings\)
+ [Editing broker engine version, instance type, CloudWatch logs, and maintenance preferences](amazon-mq-editing-broker-preferences.md)
+ [Creating and applying ActiveMQ broker configurations](amazon-mq-creating-applying-configurations.md)
+ [Editing ActiveMQ broker configurations and managing configuration revisions](amazon-mq-editing-managing-configurations.md)
+ [Listing Amazon MQ brokers and viewing broker details](amazon-mq-listing-brokers.md)
+ [Creating and managing ActiveMQ broker users](amazon-mq-listing-managing-users.md)
+ [Rebooting an Amazon MQ broker](amazon-mq-rebooting-broker.md)
+ [Accessing CloudWatch metrics for Amazon MQ](amazon-mq-accessing-metrics.md)

You can also begin to dive deep into [Amazon MQ best practices](amazon-mq-best-practices.md) and [Amazon MQ REST APIs](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/), and then [plan to migrate to Amazon MQ](https://docs.aws.amazon.com/amazon-mq/latest/migration-guide/)\.
