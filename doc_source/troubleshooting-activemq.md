# Troubleshooting: Amazon MQ for ActiveMQ<a name="troubleshooting-activemq"></a>

Use the information in this section to help you diagnose and resolve common issues you might encounter when working with Amazon MQ for ActiveMQ brokers\.

**Contents**
+ [I can't see general or audit logs for my broker in CloudWatch Logs even though I’ve activated logging\.](#issues-cw-logging-activemq)

## I can't see general or audit logs for my broker in CloudWatch Logs even though I’ve activated logging\.<a name="issues-cw-logging-activemq"></a>

 If you’re unable to view logs for your broker in CloudWatch Logs Logs, do the following\. 

1. Check if the IAM user who creates or reboots the broker has the `logs:CreateLogGroup` permission\. If you don't add the `CreateLogGroup` permission to a user before the user creates or reboots the broker, Amazon MQ will not create the log group\.

1. Check if you have configured a resource\-based policy to allow Amazon MQ to publish logs to CloudWatch Logs\. To allow Amazon MQ to publish logs to your CloudWatch Logs log group, configure a resource\-based policy to give Amazon MQ access to the following CloudWatch Logs API actions:
   +  [https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogStream.html](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogStream.html) – Creates a CloudWatch Logs log stream for the specified log group\. 
   +  [https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutLogEvents.html](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutLogEvents.html) – Delivers events to the specified CloudWatch Logs log stream\. 

 For more information about configuring Amazon MQ for ActiveMQ to publish logs to CloudWatch Logs, see [Configuring logging](https://docs.aws.amazon.com/amazon-mq/latest/developer-guide/configure-logging-monitoring-activemq.html)\. 