# Tutorial: Creating and Configuring an Amazon MQ Broker<a name="amazon-mq-creating-configuring-broker"></a>

A *broker* is a message broker environment running on Amazon MQ\. It is the basic building block of Amazon MQ\. The combined description of the broker instance *class* \(`m4`, `t2`\) and *size* \(`large`, `micro`\) is a *broker instance type* \(for example, `mq.m4.large`\)\. For more information, see [Broker](amazon-mq-basic-elements.md#broker)\.

The first and most common Amazon MQ task is creating a broker\. The following example shows how you can use the AWS Management Console to create and configure a broker using the AWS Management Console\.


+ [Step 1: Configure basic broker settings](#configure-basic-broker-settings-console)
+ [Step 2: \(Optional\) Configure advanced broker settings](#configure-advanced-broker-settings-console)
+ [Step 3: Finish creating the broker](#finish-creating-broker-console)

## Step 1: Configure basic broker settings<a name="configure-basic-broker-settings-console"></a>

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. Do one of the following:

   + If this is your first time using Amazon MQ, in the **Create a broker** section, type `MyBroker` for **Broker name** and then choose **Next step**\.

   + If you have created a broker before, on the **Create a broker** page, in the **Broker details** section, type `MyBroker` for **Broker name**\.

1. Choose a **Broker instance type** \(for example, **mq\.m4\.large**\)\. For more information, see [Instance Types](amazon-mq-basic-elements.md#broker-instance-types)\.

1. Choose a **Deployment mode**:

   + A **Single\-instance broker** is comprised of one *broker* in one Availability Zone\. The broker communicates with your application and with an AWS storage location\. For more information, see [Single\-Instance Broker](single-broker-deployment.md)\.

   + An **Active/standby broker for high availability** is comprised of two *brokers* in two different Availability Zones, configured in a *redundant pair*\. These brokers communicate with your application, and with a shared storage location\. For more information, see [Active/Standby Broker for High Availability](active-standby-broker-deployment.md)\.
**Note**  
Currently, Amazon MQ supports only the `ActiveMQ` broker engine, version `5.15.0`\.

1. In the **ActiveMQ Web Console access** section, type a **Username** and **Password**\.

## Step 2: \(Optional\) Configure advanced broker settings<a name="configure-advanced-broker-settings-console"></a>

1. Expand the **Advanced settings** section\.

1. In the **Configuration** section, choose **Create a new configuration with default values** or **Select an existing configuration**\. For more information, see [Configuration](amazon-mq-basic-elements.md#configuration) and [Amazon MQ Broker Configuration Parameters](amazon-mq-broker-configuration-parameters.md)\.

1. In the **Network and security section**, configure your broker's connectivity:

   1. Select the default **Virtual Private Cloud \(VPC\)** or create a new one on the Amazon VPC console\. For more information, see [What is Amazon VPC?](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Introduction.html) in the *Amazon VPC User Guide*\.

   1. Select the default **Subnets** or create new ones on the Amazon VPC console\. For more information, see [VPCs and Subnets](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Subnets.html) in the *Amazon VPC User Guide*\.
**Note**  
A single\-instance broker requires one subnet \(for example, the default subnet\)\. An active/standby broker for high availability requires two subnets\.

   1. Select your **Security group\(s\)**\.
**Note**  
Both single\-instance brokers and active/standby brokers for high availability require at least one security group \(for example, the default security group\)\.

   1. Choose the **Public accessibility** of your broker\.
**Note**  
Disabling public accessibility makes the broker accessible only within your VPC\. For more information, see [Always Create Brokers inside a Virtual Private Cloud \(VPC\)](amazon-mq-best-practices.md#always-create-brokers-in-vpc)\.

1. In the **Maintenance section**, configure your broker's maintenance schedule:

   1. To upgrade the broker to new versions as Apache releases them, choose **Enable automatic minor version upgrades**\. Automatic upgrades occur during the *maintenance window*, defined by the day of the week, the time of day \(in 24\-hour format\), and the time zone \(UTC by default\)\.

   1. Do one of the following:

      + To allow Amazon MQ to select the maintenance window automatically, choose **No preference**\.

      + To set a custom maintenance window, choose **Select maintenance window** and then specify the **Start day** and **Start time** of the upgrades\.

## Step 3: Finish creating the broker<a name="finish-creating-broker-console"></a>

1. Choose **Create broker**\.

   While Amazon MQ creates your broker, it displays the **Creation in progress** status\. 

   Creating the broker takes about 10 minutes\.

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

**Note**  
For an active/standby broker for high availability, Amazon MQ provides two ActiveMQ Web Console URLs, but only one URL is active at a time\. Likewise, Amazon MQ provides two endpoints for each wire\-level protocol, but only one endpoint is active in each pair at a time\. The `-1` and `-2` suffixes denote a redundant pair\. For more information, see [Amazon MQ Broker Architecture](amazon-mq-broker-architecture.md)\)\.  
For wire\-level protocol endpoints, you can allow your application to connect to either endpoint by using the [Failover Transport](http://activemq.apache.org/failover-transport-reference.html)\.