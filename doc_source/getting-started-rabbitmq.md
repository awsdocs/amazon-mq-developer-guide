# Creating and connecting to a RabbitMQ broker<a name="getting-started-rabbitmq"></a>

A *broker* is a message broker environment running on Amazon MQ\. It is the basic building block of Amazon MQ\. The combined description of the broker instance *class* \(`m5`, `t3`\) and *size* \(`large`, `micro`\) is a *broker instance type* \(for example, `mq.m5.large`\)\.

**Topics**
+ [Step 1: create a RabbitMQ broker](#create-rabbitmq-broker)
+ [Step 2: connect a JVM\-based application to your broker](#rabbitmq-connect-jvm-application)
+ [Step 3: delete your broker](#w39aab9c15c11)
+ [Next steps](#next-steps-rabbitmq-tutorials)

## Step 1: create a RabbitMQ broker<a name="create-rabbitmq-broker"></a>

The first and most common Amazon MQ task is creating a broker\. The following example shows how you can use the AWS Management Console to create a basic broker\.

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. On the **Select broker engine** page, choose **RabbitMQ**, and then choose **Next**\.

1. On the **Select deployment mode** page, choose the **Deployment mode**, for example, **Cluster deployment**, and then choose **Next**\. 
   + A **single\-instance broker** is comprised of one broker in one Availability Zone\. The broker communicates with your application and with with Amazon EBS\. Single\-instance brokers provide high throughput and lower latency\. For more information, see [Single\-instance broker](rabbitmq-broker-architecture-single-instance.md)\.
   + A **RabbitMQ cluster deployment for high availability** is a logical grouping of three RabbitMQ broker nodes behind a Network Load Balancer \(NLB\), each sharing users, queues, and a distributed state across multiple Availability Zones \(AZ\)\. For more information, see [Cluster deployment for high availability](rabbitmq-broker-architecture-cluster.md)\.

1. On the **Configure settings** page, in the **Details** section, the following:

   1. Enter the Broker name\.

   1. Choose the **Broker instance type** \(for example, **mq\.m5\.large**\)\. For more information, see [Instance types](broker-instance-types.md)\.
**Note**  
The **Additional settings** section provides options to enable CloudWatch logs and configure network access for your broker\. If you create a private RabbitMQ broker without public accessibility, you must select a Virtual Private Cloud \(VPC\) and configure a security group to access your broker\.

1. On the **Configure settings** page, in the **RabbitMQ access** section, provide a **Username** and **Password**\.

1. Choose **Next**\.

1. On the **Review and create** page, you can reivew your selections and edit them as needed\.

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

   Also, note your broker's [secure\-AMQP **Endpoints**](https://www.rabbitmq.com/connections.html)\. The following is an example of an OpenWire endpoint:

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

Use the following example to create an instance of the `ConnectionFactory` class with the given parameters\.

```
ConnectionFactory factory = new ConnectionFactory();
// "guest"/"guest" by default, limited to localhost connections
factory.setUsername(userName);
factory.setPassword(password);
factory.setVirtualHost(virtualHost);
factory.setHost(hostName);
factory.setPort(portNumber);

// Create a connection
Connection conn = factory.newConnection();
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

#### Subscribe to an exchange and recieve a message<a name="rabbitmq-subscribe-recieve-message"></a>

You can receive a message by configuring a subscription using the `Consumer` interface\. Once subscribed, messages will then be delivered automatically as they arrive\.

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

In order disconnect from your RabbitMQ broker, close both the chanel and connection as shown in the following\.

```
channel.close();
conn.close();
```

**Note**  
For more information about working with the RabbitMQ Java client library, see the RabbitMQ [Java Client API Guide](https://www.rabbitmq.com/api-guide.html)\.

## Step 3: delete your broker<a name="w39aab9c15c11"></a>

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