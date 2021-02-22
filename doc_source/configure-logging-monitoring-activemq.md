# Configuring ActiveMQ logs<a name="configure-logging-monitoring-activemq"></a>

To allow Amazon MQ to publish logs to CloudWatch Logs, you must [add a permission to your Amazon MQ user](#security-logging-monitoring-configure-cloudwatch-permissions) and also [configure a resource\-based policy for Amazon MQ](#security-logging-monitoring-configure-cloudwatch-resource-permissions) before you create or restart the broker\.

The following describes the steps to configure CloudWatch logs for your ActiveMQ brokers\.

**Topics**
+ [Understanding the structure of logging in CloudWatch Logs](#security-logging-monitoring-configure-cloudwatch-structure)
+ [Add the CreateLogGroup Permission to Your Amazon MQ User](#security-logging-monitoring-configure-cloudwatch-permissions)
+ [Configure a Resource\-Based Policy for Amazon MQ](#security-logging-monitoring-configure-cloudwatch-resource-permissions)
+ [Troubleshooting CloudWatch Logs Configuration](#security-logging-monitoring-configure-cloudwatch-troubleshoot)

## Understanding the structure of logging in CloudWatch Logs<a name="security-logging-monitoring-configure-cloudwatch-structure"></a>

You can enable *general* and *audit* logging when you [configure advanced broker settings](amazon-mq-creating-configuring-broker.md#configure-advanced-broker-settings-console) when you create a broker, or when you edit a broker\.

General logging enables the default `INFO` logging level \(`DEBUG` logging isn't supported\) and publishes `activemq.log` to a log group in your CloudWatch account\. The log group has a format similar to the following:

```
/aws/amazonmq/broker/b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9/general
```

[Audit logging](http://activemq.apache.org/audit-logging.html) enables logging of management actions taken using JMX or using the ActiveMQ Web Console and publishes `audit.log` to a log group in your CloudWatch account\. The log group has a format similar to the following:

```
/aws/amazonmq/broker/b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9/audit
```

Depending on whether you have a [single\-instance broker](single-broker-deployment.md) or an [active/standby broker](active-standby-broker-deployment.md), Amazon MQ creates either one or two log streams within each log group\. The log streams have a format similar to the following\.

```
activemq-b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.log
activemq-b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-2.log
```

The `-1` and `-2` suffixes denote individual broker instances\. For more information, see [Working with Log Groups and Log Streams](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html) in the *[Amazon CloudWatch Logs User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/)*\. 

## Add the CreateLogGroup Permission to Your Amazon MQ User<a name="security-logging-monitoring-configure-cloudwatch-permissions"></a>

To allow Amazon MQ to create a CloudWatch Logs log group, you must ensure that the IAM user who creates or reboots the broker has the `logs:CreateLogGroup` permission\.

**Important**  
If you don't add the `CreateLogGroup` permission to your Amazon MQ user before the user creates or reboots the broker, Amazon MQ doesn't create the log group\.

The following example [IAM\-based policy](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/iam-access-control-overview-cwl.html#identity-based-policies-cwl) grants permission for `logs:CreateLogGroup` for users to whom this policy is attached\.

```
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Effect": "Allow",
         "Action": "logs:CreateLogGroup",
         "Resource": "arn:aws:logs:*:*:log-group:/aws/amazonmq/*"
      }
   ]
}
```

**Note**  
Here, the term user refers to *IAM Users* and not *Amazon MQ users*, which are created when a new broker is configured\. For more information regarding setting up IAM users and configuring IAM policies, please refer to the [Identity Management Overview](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_identity-management.html) section of the IAM User Guide\. 

For more information, see `[CreateLogGroup](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogGroup.html)` in the *Amazon CloudWatch Logs API Reference*\.

## Configure a Resource\-Based Policy for Amazon MQ<a name="security-logging-monitoring-configure-cloudwatch-resource-permissions"></a>

To allow Amazon MQ to publish logs to your CloudWatch Logs log group, configure a resource\-based policy to give Amazon MQ access to the following CloudWatch Logs API actions:
+ `[CreateLogStream](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogStream.html)` – Creates a CloudWatch Logs log stream for the specified log group\.
+ `[PutLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutLogEvents.html)` – Delivers events to the specified CloudWatch Logs log stream\.

**Important**  
If you don't configure a resource\-based policy for Amazon MQ, the broker can't publish the logs to CloudWatch Logs\.

This resource\-based policy *must* be configured by using the AWS CLI\. The following command grants permission for `logs:CreateLogStream` and `logs:PutLogEvents` to AWS\.

```
aws --region us-east-1 logs put-resource-policy --policy-name AmazonMQ-ActiveMQ-logs \
        		--policy-document '{ "Version": "2012-10-17", "Statement": [ {
        		"Effect": "Allow", "Principal": { "Service": "mq.amazonaws.com" },
        		"Action": [ "logs:CreateLogStream", "logs:PutLogEvents" ],
        		"Resource": "arn:aws:logs:*:*:log-group:/aws/amazonmq/*" } ]}'
```

**Note**  
Because this example uses the `/aws/amazonmq/` prefix, you need to configure the resource\-based policy only once per AWS account, per region\.

## Troubleshooting CloudWatch Logs Configuration<a name="security-logging-monitoring-configure-cloudwatch-troubleshoot"></a>

In some cases, CloudWatch Logs might not always behave as expected\. This section gives an overview of common issues and shows how to resolve them\.

### Log Groups Don't Appear in CloudWatch<a name="security-logging-monitoring-configure-cloudwatch-do-not-appear"></a>

[Add the `CreateLogGroup` permission to your Amazon MQ user](#security-logging-monitoring-configure-cloudwatch-permissions) and reboot the broker\. This allows Amazon MQ to create the log group\.

### Log Streams Don't Appear in CloudWatch Log Groups<a name="security-logging-monitoring-configure-cloudwatch-streams-do-not-appear"></a>

[Configure a resource\-based policy for Amazon MQ](#security-logging-monitoring-configure-cloudwatch-resource-permissions)\. This allows your broker to publish its logs\.