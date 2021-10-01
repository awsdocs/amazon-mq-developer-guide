# Creating and connecting to a RabbitMQ broker<a name="getting-started-rabbitmq"></a>

A *broker* is a message broker environment running on Amazon MQ\. It is the basic building block of Amazon MQ\. The combined description of the broker instance *class* \(`m5`, `t3`\) and *size* \(`large`, `micro`\) is a *broker instance type* \(for example, `mq.m5.large`\)\.

**Topics**
+ [Step 1: create a RabbitMQ broker](#create-rabbitmq-broker)
+ [Step 2: connect a JVM\-based application to your broker](#rabbitmq-connect-jvm-application)
+ [Connect your Amazon MQ for RabbitMQ broker to Lambda](#rabbitmq-connect-to-lambda)
+ [Step 4: delete your broker](#rabbitmq-delete-broker)
+ [Next steps](#next-steps-rabbitmq-tutorials)

## Step 1: create a RabbitMQ broker<a name="create-rabbitmq-broker"></a>

The first and most common Amazon MQ task is creating a broker\. The following example shows how you can use the AWS Management Console to create a basic broker\.

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. On the **Select broker engine** page, choose **RabbitMQ**, and then choose **Next**\.

1. On the **Select deployment mode** page, choose the **Deployment mode**, for example, **Cluster deployment**, and then choose **Next**\. 
   + A **single\-instance broker** is comprised of one broker in one Availability Zone behind a Network Load Balancer \(NLB\)\. The broker communicates with your application and with an Amazon EBS storage volume\. For more information, see [Single\-instance broker](rabbitmq-broker-architecture-single-instance.md)\.
   + A **RabbitMQ cluster deployment for high availability** is a logical grouping of three RabbitMQ broker nodes behind a Network Load Balancer, each sharing users, queues, and a distributed state across multiple Availability Zones \(AZ\)\. For more information, see [Cluster deployment for high availability](rabbitmq-broker-architecture-cluster.md)\.

1. On the **Configure settings** page, in the **Details** section, the following:

   1. Enter the Broker name\.
**Important**  
 We recommend not using any personally identifiable information in broker names\. 

   1. Choose the **Broker instance type** \(for example, **mq\.m5\.large**\)\. For more information, see [Instance types](broker-instance-types.md)\.
**Note**  
The **Additional settings** section provides options to enable CloudWatch logs and configure network access for your broker\. If you create a private RabbitMQ broker without public accessibility, you must select a Virtual Private Cloud \(VPC\) and configure a security group to access your broker\.

1. On the **Configure settings** page, in the **RabbitMQ access** section, provide a **Username** and **Password**\.
**Important**  
Your username can contain only alphanumeric characters, dashes, periods, and underscores \(\- \. \_\)\. This value must not contain any tilde \(\~\) characters\. Amazon MQ prohibits using `guest` as a username\. We strongly recommend that you never use any personally identifiable information in broker usernames\.
 Your password must be at least 12 characters long, contain at least 4 unique characters and must not contain commas, colons, or equal signs \(,:=\)\.

1. Choose **Next**\.

1. On the **Review and create** page, you can review your selections and edit them as needed\.

1. Choose **Create broker**\.

   While Amazon MQ creates your broker, it displays the **Creation in progress** status\. 

   Creating the broker takes about 15 minutes\.

   When your broker is created successfully, Amazon MQ displays the **Running** status\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-getting-started-create-broker-running.png)

1. Choose ***MyBroker***\.

   On the ***MyBroker*** page, in the **Connect** section, note your broker's **[RabbitMQ web console](https://www.rabbitmq.com/management.html)** URL, for example:

   ```
   https://b-c8349341-ec91-4a78-ad9c-a57f23f235bb.mq.us-west-2.amazonaws.com
   ```

   Also, note your broker's [secure\-AMQP **Endpoint**](https://www.rabbitmq.com/connections.html)\. The following is an example of an `amqps` endpoint exposing listener port `5671`\.

   ```
   amqps://b-c8349341-ec91-4a78-ad9c-a57f23f235bb.mq.us-west-2.amazonaws.com:5671
   ```

## Step 2: connect a JVM\-based application to your broker<a name="rabbitmq-connect-jvm-application"></a>

 After you create a RabbitMQ broker, you can connect your application to it\. The following examples show how you can use the [RabbitMQ Java client library](https://www.rabbitmq.com/java-client.html) to create a connection to your broker, create a queue, and send a message\. You can connect to RabbitMQ brokers using supported RabbitMQ client libraries for a variety of languages\. For more information about supported RabbitMQ client libraries, see [RabbitMQ client libraries and developer tools](https://www.rabbitmq.com/devtools.html)\. 

### Prerequisites<a name="rabbitmq-connect-application-prerequisites-getting-started"></a>

**Note**  
The following prerequisite steps are only applicable to RabbitMQ brokers created without public accessibility\. If you are creating a broker with public accessibility you can skip them\.

#### Enable VPC attributes<a name="rabbitmq-connect-application-enable-vpc-attributes-getting-started"></a>

To ensure that your broker is accessible within your VPC, you must enable the `enableDnsHostnames` and `enableDnsSupport` VPC attributes\. For more information, see [DNS Support in your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-support) in the *Amazon VPC User Guide*\.

#### Enable inbound connections<a name="rabbitmq-connect-application-allow-inbound-connections-getting-started"></a>

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

   1. For **Source**, leave **Custom** selected and then type the IP address of the system that you want to be able to access the web console \(for example, `192.0.2.1`\)\.

   1. Choose **Save**\.

      Your broker can now accept inbound connections\.

#### Add Java dependencies<a name="rabbitmq-connect-application-java-dependencies-getting-started"></a>

If you are using Apache Maven for automating builds, add the following dependency to your `pom.xml` file\. For more information about Project Object Model files in Apache Maven, see [Introduction to the POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)\.

```
<dependency>
    <groupId>com.rabbitmq</groupId>
    <artifactId>amqp-client</artifactId>
    <version>5.9.0</version>
</dependency>
```

If you are using [Gradle](https://docs.gradle.org/current/userguide/userguide.html) for automating builds, declare the following dependency\.

```
dependencies {
    compile 'com.rabbitmq:amqp-client:5.9.0'
}
```

#### Import `Connection` and `Channel` classes<a name="rabbitmq-import-connections-and-channels"></a>

 RabbitMQ Java client uses `com.rabbitmq.client` as its top\-level package, with `Connection` and `Channel` API classes representing an AMQP 0\-9\-1 connection and channel, respectively\. Import the `Connection` and `Channel` classes before using them, as shown in the following example\. 

```
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.Channel;
```

#### Create a `ConnectionFactory` and connect to your broker<a name="rabbitmq-create-connection-factory-and-connect"></a>

Use the following example to create an instance of the `ConnectionFactory` class with the given parameters\. Use the `setHost` method to configure the broker endpoint you noted earlier\. For `AMQPS` wire\-level connections, use port `5671`\.

```
ConnectionFactory factory = new ConnectionFactory();

factory.setUsername(username);
factory.setPassword(password);

//Replace the URL with your information
factory.setHost("b-c8352341-ec91-4a78-ad9c-a43f23d325bb.mq.us-west-2.amazonaws.com");
factory.setPort(5671);

// Allows client to establish a connection over TLS
factory.useSslProtocol()

// Create a connection
Connection conn = factory.newConnection();

// Create a channel
Channel channel = conn.createChannel();
```

#### Publish a message to an exchange<a name="rabbitmq-publish-message"></a>

 You can use `Channel.basicPublish` to publish messages to an exchange\. The following example uses the AMQP `Builder` class to build a message properties object with content\-type `plain/text`\. 

```
byte[] messageBodyBytes = "Hello, world!".getBytes();
channel.basicPublish(exchangeName, routingKey,
             new AMQP.BasicProperties.Builder()
               .contentType("text/plain")
               .userId("userId")
               .build(),
               messageBodyBytes);
```

**Note**  
Note that `BasicProperties` is an inner class of the autogenerated holder class, `AMQP`\.

#### Subscribe to a queue and receive a message<a name="rabbitmq-subscribe-receive-message"></a>

You can receive a message by subscribing to a queue using the `Consumer` interface\. Once subscribed, messages will then be delivered automatically as they arrive\.

The easiest way to implement a `Consumer` is to use the subclass `DefaultConsumer`\. A `DefaultConsumer` object can be passed as part of a `basicConsume` call to set up the subscription as shown in the following example\.

```
boolean autoAck = false;
channel.basicConsume(queueName, autoAck, "myConsumerTag",
     new DefaultConsumer(channel) {
         @Override
         public void handleDelivery(String consumerTag,
                                    Envelope envelope,
                                    AMQP.BasicProperties properties,
                                    byte[] body)
             throws IOException
         {
             String routingKey = envelope.getRoutingKey();
             String contentType = properties.getContentType();
             long deliveryTag = envelope.getDeliveryTag();
             // (process the message components here ...)
             channel.basicAck(deliveryTag, false);
         }
     });
```

**Note**  
Because we specified `autoAck = false`, it is necessary to acknowledge messages delivered to the `Consumer`, most conveniently done in the `handleDelivery` method, as shown in the example\.

#### Close your connection and disconnect from the broker<a name="rabbitmq-disconnect"></a>

In order to disconnect from your RabbitMQ broker, close both the channel and connection as shown in the following\.

```
channel.close();
conn.close();
```

**Note**  
For more information about working with the RabbitMQ Java client library, see the [RabbitMQ Java Client API Guide](https://www.rabbitmq.com/api-guide.html)\.

## Step 3: \(Optional\) connect to an AWS Lambda function<a name="rabbitmq-connect-to-lambda"></a>

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

1.  Write some code for your Lambda function to process the messages from your consumed from your broker\. The Lambda payload that retrieved by your event source mapping depends on the engine type of the broker\. The following is an example of a Lambda payload for an Amazon MQ for RabbitMQ queue\. 
**Note**  
 In the example, `test` is the name of the queue, and `/` is the name of the default virtual host\. When receiving messages, the event source lists messages under `test::/`\. 

   ```
   {
     "eventSource": "aws:rmq",
     "eventSourceArn": "arn:aws:mq:us-west-2:112556298976:broker:test:b-9bcfa592-423a-4942-879d-eb284b418fc8",
     "rmqMessagesByQueue": {
       "test::/": [
         {
           "basicProperties": {
             "contentType": "text/plain",
             "contentEncoding": null,
             "headers": {
               "header1": {
                 "bytes": [
                   118,
                   97,
                   108,
                   117,
                   101,
                   49
                 ]
               },
               "header2": {
                 "bytes": [
                   118,
                   97,
                   108,
                   117,
                   101,
                   50
                 ]
               },
               "numberInHeader": 10
             }
             "deliveryMode": 1,
             "priority": 34,
             "correlationId": null,
             "replyTo": null,
             "expiration": "60000",
             "messageId": null,
             "timestamp": "Jan 1, 1970, 12:33:41 AM",
             "type": null,
             "userId": "AIDACKCEVSQ6C2EXAMPLE",
             "appId": null,
             "clusterId": null,
             "bodySize": 80
           },
           "redelivered": false,
           "data": "eyJ0aW1lb3V0IjowLCJkYXRhIjoiQ1pybWYwR3c4T3Y0YnFMUXhENEUifQ=="
         }
       ]
     }
   }
   ```

For more information about connecting Amazon MQ to Lambda, the options Lambda supports for an Amazon MQ event source, and event source mapping errors, see [Using Lambda with Amazon MQ](https://docs.aws.amazon.com/lambda/latest/dg/with-mq.html) in the *AWS Lambda Developer Guide*\.

## Step 4: delete your broker<a name="rabbitmq-delete-broker"></a>

If you don't use an Amazon MQ broker \(and don't foresee using it in the near future\), it is a best practice to delete it from Amazon MQ to reduce your AWS costs\.

The following example shows how you can delete a broker using the AWS Management Console\.

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. From the broker list, select your broker \(for example, **MyBroker**\) and then choose **Delete**\.

1. In the **Delete *MyBroker*?** dialog box, type `delete` and then choose **Delete**\.

   Deleting a broker takes about 5 minutes\.

## Next steps<a name="next-steps-rabbitmq-tutorials"></a>

Now that you have created a broker, connected an application to it, and sent and received a message, you might want to try the following:
+ [Editing broker engine version, instance type, CloudWatch logs, and maintenance preferences](amazon-mq-editing-broker-preferences.md)
+ [Listing Amazon MQ brokers and viewing broker details](amazon-mq-listing-brokers.md)
+ [Creating and managing ActiveMQ broker users](amazon-mq-listing-managing-users.md)
+ [Rebooting an Amazon MQ broker](amazon-mq-rebooting-broker.md)
+ [Accessing CloudWatch metrics for Amazon MQ](amazon-mq-accessing-metrics.md)

You can also begin to dive deep into [Amazon MQ best practices](amazon-mq-best-practices.md) and [Amazon MQ REST APIs](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/) before planning to migrate to Amazon MQ\.