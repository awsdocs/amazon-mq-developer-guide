# Troubleshooting: Amazon MQ for RabbitMQ<a name="troubleshooting-rabbitmq"></a>

Use the information in this section to help you diagnose and resolve common issues you might encounter when working with Amazon MQ for RabbitMQ brokers\.

**Contents**
+ [I can’t see metrics for my queues or virtual hosts in CloudWatch\.](#issues-cw-metrics-rabbitmq)

## I can’t see metrics for my queues or virtual hosts in CloudWatch\.<a name="issues-cw-metrics-rabbitmq"></a>

 If you’re unable to view metrics for your queues or metrics in CloudWatch, check if your queue or virtual host names contain any blank spaces, tabs, or other non\-ASCII characters\. 

Amazon MQ cannot publish metrics for virtual hosts and queues with names containing blank spaces, tabs or other non\-ASCII characters\.

For more information about dimension names, see [Dimension](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_Dimension.html#API_Dimension_Contents) in the *Amazon CloudWatch API Reference*\. 