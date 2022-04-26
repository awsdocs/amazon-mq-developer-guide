# Troubleshooting: Amazon MQ action required codes<a name="troubleshooting-action-required-codes"></a>

 Amazon MQ returns an exception for certain API operations, such as [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id-reboot.html](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id-reboot.html), if your broker is in an unhealthy state and requires a set of actions to return to a healthy state\. The exceptions include specific *action required codes* that help you to identify a root cause, and address the issue and recover your broker\. 

Use the following list of topics to identify the action required code you have received, and learn more about the steps we recommend to resolve your issue\.

**Topics**
+ [`RABBITMQ_MEMORY_ALARM`](troubleshooting-action-required-codes-rabbitmq-memory-alarm.md)