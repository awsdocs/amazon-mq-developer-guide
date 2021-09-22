# Troubleshooting: Amazon MQ for ActiveMQ<a name="troubleshooting-activemq"></a>

Use the information in this section to help you diagnose and resolve common issues you might encounter when working with Amazon MQ for ActiveMQ brokers\.

**Contents**
+ [I can't see general or audit logs for my broker in CloudWatch Logs even though I’ve activated logging\.](#issues-cw-logging-activemq)
+ [After broker restart or maintenance window, I can't connect to my broker even though the status is `RUNNING`\. Why?](#issues-connection-after-restart)
+ [I'm seeing exception `org.apache.jasper.JasperException: An exception occurred processing JSP page` on the ActiveMQ console when performing operations\.](#issues-jsp-exception)

## I can't see general or audit logs for my broker in CloudWatch Logs even though I’ve activated logging\.<a name="issues-cw-logging-activemq"></a>

 If you’re unable to view logs for your broker in CloudWatch Logs Logs, do the following\. 

1. Check if the IAM user who creates or reboots the broker has the `logs:CreateLogGroup` permission\. If you don't add the `CreateLogGroup` permission to a user before the user creates or reboots the broker, Amazon MQ will not create the log group\.

1. Check if you have configured a resource\-based policy to allow Amazon MQ to publish logs to CloudWatch Logs\. To allow Amazon MQ to publish logs to your CloudWatch Logs log group, configure a resource\-based policy to give Amazon MQ access to the following CloudWatch Logs API actions:
   +  [https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogStream.html](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogStream.html) – Creates a CloudWatch Logs log stream for the specified log group\. 
   +  [https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutLogEvents.html](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutLogEvents.html) – Delivers events to the specified CloudWatch Logs log stream\. 

 For more information about configuring Amazon MQ for ActiveMQ to publish logs to CloudWatch Logs, see [Configuring logging](https://docs.aws.amazon.com/amazon-mq/latest/developer-guide/configure-logging-monitoring-activemq.html)\. 

## After broker restart or maintenance window, I can't connect to my broker even though the status is `RUNNING`\. Why?<a name="issues-connection-after-restart"></a>

 You might be encountering connection issues after a broker restart you initiated, after a scheduled maintenance window is completed, or in a failure event, where the standby instance is activated\. In either case, connection issues following a broker restart are most likely caused by unusually large numbers of messages persisted in your broker's Amazon EFS or Amazon EBS storage volume\. During a restart, Amazon MQ moves persisted messages from storage to broker memory\. To confirm this diagnosis, you can monitor the following metrics on CloudWatch for your Amazon MQ for ActiveMQ broker: 
+  **`StoragePercentUsage`** — Large percentages at or close to 100 percent can cause the broker to refuse connections\. 
+  **`JournalFilesForFullRecovery`** — Indicates the number of of journal files that will be replayed following an unclean shutdown and restart\. An increasing, or consistently higher than one, value indicates unresolved transactions that can cause connection issues after restart\. 
+  **`OpenTransactionCount`** — A number larger than zero following a restart indicates that the broker will attempt to store previously consumed messages, as a result causing connection issues\. 

 To resolve this issue, we recommend resolving your XA transactions with either a `rollback()` or a `commid()`\. For more information, and to see a code example of resolving XA transactions using `rollback()`, see [recovering XA transactions](recover-xa-transactions.md)\. 

## I'm seeing exception `org.apache.jasper.JasperException: An exception occurred processing JSP page` on the ActiveMQ console when performing operations\.<a name="issues-jsp-exception"></a>

 If you are using simple authentication and configuring `AuthorizationPlugin` for queue and topic authorization, make sure to use the `AuthorizationEntries` element in your XML configuration file, and allow the `activmemq.webconsole` group permission to all queues and topics\. This ensures that the ActiveMQ web console can communicate with the ActiveMQ broker\. 

 The following example `AuthorizationEntry` grants read and write permissions for all queues and topics to the `activemq.webconsole` group\. 

```
<authorizationEntries>
    <authorizationEntry admin="activemq-webconsole,admins,users" topic=">" read="activemq-webconsole,admins,users" write="activemq-webconsole,admins,users" />
    <authorizationEntry admin="activemq-webconsole,admins,users" queue=">" read="activemq-webconsole,admins,users" write="activemq-webconsole,admins,users" />
</authorizationEntries>
```

 Similarly when integrating your broker with LDAP, make sure to grant permission for the `amazonmq-console-admins` group\. For more information about LDAP integration, see [How LDAP integration works](security-authentication-authorization.md#ldap-support-details)\. 