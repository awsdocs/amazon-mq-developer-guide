# Configuring Amazon MQ to Publish General and Audit Logs to Amazon CloudWatch Logs<a name="amazon-mq-configuring-cloudwatch-logs"></a>

Amazon MQ is integrated with Amazon CloudWatch Logs, a service that monitors, stores, and accesses your log files from a variety of sources\. For example, you can [configure CloudWatch alarms](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/AlarmThatSendsEmail.html) to receive notifications of [broker reboots](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker-reboot.html) or troubleshoot [broker configuration](amazon-mq-broker-configuration-parameters.md) errors\. For more information about CloudWatch Logs, see the *[Amazon CloudWatch Logs User Guide](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/)*\.

To allow Amazon MQ to publish logs to CloudWatch Logs, you must [add a permission to your Amazon MQ user](#add-createloggroup-permission-to-user) and also [configure a resource\-based policy for Amazon MQ](#configure-resource-based-policy) before you create or restart the broker\.

For more information about configuring Amazon MQ to publish general and audit logs to CloudWatch Logs, see [Configure Advanced Broker Settings](amazon-mq-creating-configuring-broker.md#configure-advanced-broker-settings-console)\.

**Topics**
+ [Structure of Logging in CloudWatch Logs](#structure-of-logging-cloudwatch-logs)
+ [Add the CreateLogGroup Permission to Your Amazon MQ User](#add-createloggroup-permission-to-user)
+ [Configure a Resource\-Based Policy for Amazon MQ](#configure-resource-based-policy)

## Structure of Logging in CloudWatch Logs<a name="structure-of-logging-cloudwatch-logs"></a>

You can enable *general* and *audit* logging when you [configure advanced broker settings](amazon-mq-creating-configuring-broker.md#configure-advanced-broker-settings-console) when you create a broker, or when you edit a broker\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-tutorials-enable-cloudwatch-logs-edit-broker.png)

General logging enables the default `INFO` logging level \(`DEBUG` logging isn't supported\) and publishes `activemq.log` to a log group in your CloudWatch account\. The log group has a format similar to the following:

```
/aws/amazonmq/broker/b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9/general
```

[Audit logging](http://activemq.apache.org/audit-logging.html) enables logging of management actions taken using JMX or using the ActiveMQ Web Console and publishes `audit.log` to a log group iin your CloudWatch account\. The log group has a format similar to the following:

```
/aws/amazonmq/broker/b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9/audit
```

Depending on whether you have a [single\-instance broker](single-broker-deployment.md) or an [active/standby broker for high availability](active-standby-broker-deployment.md), Amazon MQ creates either one or two log streams within each log group\. The log streams have a format similar to the following\.

```
activemq-b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.log
activemq-b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-2.log
```

The `-1` and `-2` suffixes denote individual broker instances\. For more information, see [Working with Log Groups and Log Streams]() in the *[Amazon CloudWatch Logs User Guide](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/)*\. 

## Add the CreateLogGroup Permission to Your Amazon MQ User<a name="add-createloggroup-permission-to-user"></a>

To allow Amazon MQ to create a CloudWatch Logs log group, you must ensure that the user who creates or reboots the broker has the `logs:CreateLogGroup` permission\.

**Important**  
If you don't add the `CreateLogGroup` permission to your Amazon MQ user before the user creates or reboots the broker, Amazon MQ doesn't create the log group\.

The following example [IAM\-based policy](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/iam-access-control-overview-cwl.html#identity-based-policies-cwl) grants permission for `logs:CreateLogGroup` to user 111122223333\.

```
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Effect": "Allow",
         "Principal": {
            "AWS": "111122223333"
         },
         "Action": "logs:CreateLogGroup",
         "Resource": "arn:aws:logs:*:*:log-group:/aws/amazonmq/*"
      }
   ]
}
```

For more information, see `[CreateLogGroup](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogGroup.html)` in the *Amazon CloudWatch Logs API Reference*\.

## Configure a Resource\-Based Policy for Amazon MQ<a name="configure-resource-based-policy"></a>

To allow Amazon MQ to publish logs to your CloudWatch Logs log group, configure a resource\-based policy to give Amazon MQ access to the following CloudWatch Logs API actions:
+ `[CreateLogStream](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogStream.html)` – Creates a CloudWatch Logs log stream for the specified log group\.
+ `[PutLogEvents](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutLogEvents.html)` – Delivers events to the specified CloudWatch Logs log stream\.

**Important**  
If you don't configure a resource\-based policy for Amazon MQ, the broker can't post the logs\.

The following example [resource\-based policy](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/iam-access-control-overview-cwl.html#resource-based-policies-cwl) grants permission for `logs:CreateLogStream` and `logs:PutLogEvents` to AWS\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "mq.amazonaws.com"
      },
      "Action":[
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource" : "arn:aws:logs:*:*:log-group:/aws/amazonmq/*"
    }
  ]
}
```

**Note**  
Because this example uses the `/aws/amazonmq/` prefix, you need to configure the resource\-based policy only once per AWS account, per region\.

You can achieve the same effect using the following AWS CLI command:

```
aws --region us-east-1 logs put-resource-policy --policy-name AmazonMQ-logs \
	--policy-document '{ "Version": "2012-10-17", "Statement": [ { 
	"Effect": "Allow", "Principal": { "Service": "mq.amazonaws.com" }, 
	"Action":[ "logs:CreateLogStream", "logs:PutLogEvents" ],
	"Resource" : "arn:aws:logs:*:*:log-group:/aws/amazonmq/*" } ] }'
```