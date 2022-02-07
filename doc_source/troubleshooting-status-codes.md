# Troubleshooting: Amazon MQ status codes<a name="troubleshooting-status-codes"></a>

 Amazon MQ returns an exception for certain API operations, such as [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id-reboot.html](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id-reboot.html), if your broker is in an unhealthy state and requires recovery\. The exceptions include specific *status codes* that help you identify the root causes, and address the issue in order to recover your broker\. 

Use the following list of topics to identify the status code you have received, and learn more about the steps we recommend to resolve your issue\.

**Topics**
+ [`RABBITMQ_MEMORY_ALARM`](troubleshooting-status-codes-rabbitmq-memory-alarm.md)