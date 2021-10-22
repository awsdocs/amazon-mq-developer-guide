# Troubleshooting: Amazon MQ for RabbitMQ<a name="troubleshooting-rabbitmq"></a>

Use the information in this section to help you diagnose and resolve common issues you might encounter when working with Amazon MQ for RabbitMQ brokers\.

**Contents**
+ [I can’t see metrics for my queues or virtual hosts in CloudWatch\.](#issues-cw-metrics-rabbitmq)
+ [How do I enable plugins in Amazon MQ for RabbitMQ?](#issues-enabling-plugins-rabbitmq)
+ [I'm unable to change Amazon VPC configuration for the broker\.](#issues-changing-vpc-configration-rabbitmq)

## I can’t see metrics for my queues or virtual hosts in CloudWatch\.<a name="issues-cw-metrics-rabbitmq"></a>

 If you’re unable to view metrics for your queues or virtual hosts in CloudWatch, check if your queue or virtual host names contain any blank spaces, tabs, or other non\-ASCII characters\. 

Amazon MQ cannot publish metrics for virtual hosts and queues with names containing blank spaces, tabs or other non\-ASCII characters\.

For more information about dimension names, see [Dimension](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_Dimension.html#API_Dimension_Contents) in the *Amazon CloudWatch API Reference*\. 

## How do I enable plugins in Amazon MQ for RabbitMQ?<a name="issues-enabling-plugins-rabbitmq"></a>

 Amazon MQ for RabbitMQ currently only supports the RabbitMQ management, shovel, federation, consistent\-hash exchange plugin, which are enabled by default\. For more information on using supported plugins, see [Plugins](rabbitmq-basic-elements-plugins.md)\. 

## I'm unable to change Amazon VPC configuration for the broker\.<a name="issues-changing-vpc-configration-rabbitmq"></a>

 Amazon MQ does not support changing Amazon VPC configuration after your broker is created\. Please note that you will need to create a new broker with the new Amazon VPC configuration and update the client connection URL with the new broker connection URL\. 