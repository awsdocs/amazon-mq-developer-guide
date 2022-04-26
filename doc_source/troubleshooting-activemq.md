# Troubleshooting: Amazon MQ for ActiveMQ<a name="troubleshooting-activemq"></a>

Use the information in this section to help you diagnose and resolve common issues you might encounter when working with Amazon MQ for ActiveMQ brokers\.

**Contents**
+ [I can't see general or audit logs for my broker in CloudWatch Logs even though I’ve activated logging\.](#issues-cw-logging-activemq)
+ [After broker restart or maintenance window, I can't connect to my broker even though the status is `RUNNING`\. Why?](#issues-connection-after-restart)
+ [I see some of my clients connecting to the broker, while others are unable to connect\.](#issues-connection-limit)
+ [I'm seeing exception `org.apache.jasper.JasperException: An exception occurred processing JSP page` on the ActiveMQ console when performing operations\.](#issues-jsp-exception)

## I can't see general or audit logs for my broker in CloudWatch Logs even though I’ve activated logging\.<a name="issues-cw-logging-activemq"></a>

 If you’re unable to view logs for your broker in CloudWatch Logs, do the following\. 

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

## I see some of my clients connecting to the broker, while others are unable to connect\.<a name="issues-connection-limit"></a>

 If your broker is in the `RUNNING` status and some clients are able to connect to the broker successfully, while others are unable to do so, you may have reached the [wire\-level connections](amazon-mq-limits.md#broker-limits) limit for the broker\. To verify that you've reached the wire\-level connections limit, do the following: 
+  Check the *general* broker logs for your Amazon MQ for ActiveMQ broker in CloudWatch Logs\. If the limit has been reached, you will see `Reached Maximum Connections` in the broker logs\. For more information on CloudWatch Logs for Amazon MQ for ActiveMQ brokers, see [Understanding the structure of logging in CloudWatch Logs](configure-logging-monitoring-activemq.md#security-logging-monitoring-configure-cloudwatch-structure)\. 

Once the wire\-level connections limit is reached, the broker will actively refuse additional incoming connections\. To resolve this issue, we recommend upgrading your broker instance type\. For more information on choosing the best instance type for your workload, see [Broker instance types](broker-instance-types.md)\.

 If you've confirmed that the number of your wire\-level connections is less than the broker connection limit, the issue might be related to rebooting clients\. Check your broker logs for numerous and frequent entries of `... Inactive for longer than 600000 ms - removing ...`\. The log entry is indicative of rebooting clients or connectivity issues\. This effect is more evident when clients connect to the broker via a Network Load Balancer \(NLB\) with clients that frequently disconnect and reconnect to the broker\. This is more typically observed in container based clients\. 

 Check your client\-side logs for further details\. The broker will clean up inactive TCP connections after 600000 ms, and free up the connection socket\. 

## I'm seeing exception `org.apache.jasper.JasperException: An exception occurred processing JSP page` on the ActiveMQ console when performing operations\.<a name="issues-jsp-exception"></a>

 If you are using simple authentication and configuring `AuthorizationPlugin` for queue and topic authorization, make sure to use the `AuthorizationEntries` element in your XML configuration file, and allow the `activemq-webconsole` group permission to all queues and topics\. This ensures that the ActiveMQ web console can communicate with the ActiveMQ broker\. 

 The following example `AuthorizationEntry` grants read and write permissions for all queues and topics to the `activemq-webconsole` group\. 

```
<authorizationEntries>
    <authorizationEntry admin="activemq-webconsole,admins,users" topic=">" read="activemq-webconsole,admins,users" write="activemq-webconsole,admins,users" />
    <authorizationEntry admin="activemq-webconsole,admins,users" queue=">" read="activemq-webconsole,admins,users" write="activemq-webconsole,admins,users" />
</authorizationEntries>
```

 Similarly when integrating your broker with LDAP, make sure to grant permission for the `amazonmq-console-admins` group\. For more information about LDAP integration, see [How LDAP integration works](security-authentication-authorization.md#ldap-support-details)\. 