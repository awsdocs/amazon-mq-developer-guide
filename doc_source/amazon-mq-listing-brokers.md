# Listing Amazon MQ brokers and viewing broker details<a name="amazon-mq-listing-brokers"></a>

When you request that Amazon MQ create a broker, the creation process can take about 15 minutes\.\.

The following example shows how you can confirm your broker's existence by listing your brokers in the current region using the AWS Management Console\.

## To list brokers and view broker details<a name="listing-all-brokers-console"></a>

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

   Your brokers in the current region are listed\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-brokers-console.png)

   The following information is displayed for each broker:
   + **Name**
   + **Creation** date
   + [**Status**](broker-statuses.md)
   + **Deployment mode**
   + [**Instance type**](broker-instance-types.md)

1. Choose your broker's name \.

   For ActiveMQ brokers, on the ***MyBroker*** page, the [configured](configuration.md) **Details** are displayed for your broker:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-active-active-standby-detail-console.png)

   For RabbitMQ brokers, on the ***MyBroker2*** page, your selected settings are displayed in the **Details**section as shown below\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-rabbit-single-instance-detail-console.png)

   Below the **Details** section, the following information is displayed:
   + In the **Connections** section, for ActiveMQ brokers, the web console URL and the wire\-level protocol endpoints  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-active-connections-console.png)

     In the **Connections** section, for RabbitMQ brokers, the web console URL and the secure AMQP endpoint  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-rabbit-connections-console.png)
   + For ActiveMQ brokers, in the **Users** section, the [users](user.md) associated with the broker
**Important**  
Managing users via the AWS Management Console and the Amazon MQ API is not supported for RabbitMQ brokers\.