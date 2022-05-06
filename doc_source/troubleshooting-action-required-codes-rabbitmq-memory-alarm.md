# Amazon MQ for RabbitMQ: High memory alarm<a name="troubleshooting-action-required-codes-rabbitmq-memory-alarm"></a>

RabbitMQ will raise a high memory alarm when the broker's memory usage, identified by CloudWatch metric `RabbitMQMemUsed`, exceeds the memory limit, identified by `RabbitMQMemLimit`\. `RabbitMQMemLimit` is set by Amazon MQ and has been specifically tuned considering the memory available for each host instance type\.

An Amazon MQ for RabbitMQ broker that has raised a high memory alarm will block all clients that are publishing messages\. Due to high memory usage, your broker might also experience other issues that complicate diagnosis and resolution of the alarm\.

Single\-instance brokers that can't complete start\-up due to high memory usage might enter a restart loop, during which, interactions with the broker are limited\. In cluster deployments, queues might experience paused synchronization of messages between replicas on different nodes\. Paused queue syncs prevent consumption of messages from queues and must be addressed separately while resolving the memory alarm\.

Amazon MQ will not restart a broker experiencing a high memory alarm and will return an exception for [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id-reboot.html](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id-reboot.html) API operations as long as the broker continues to raise the alarm\.

Use the information in this section to help you diagnose and resolve RabbitMQ high memory alarms raised by your broker\.

**Topics**
+ [Diagnosing high memory alarm using the RabbitMQ web console](#diagnose-using-console)
+ [Diagnosing high memory alarm using Amazon MQ metrics](#diagnose-using-metrics)
+ [Addressing high memory alarm](#addressing-high-memory-alarm)
+ [Reducing the number of connections and channels](#reducing-connections-and-channels)
+ [Addressing paused queue synchronizations in cluster deployments](#addressing-paused-queue-sync)
+ [Addressing restart loops in single\-instance brokers](#w279aac33c15b9c25)
+ [Preventing high memory alarms](#preventing-high-memory-alarm)

## Diagnosing high memory alarm using the RabbitMQ web console<a name="diagnose-using-console"></a>

The RabbitMQ web console can generate and display detailed memory usage information for each node\. You can find this information by doing the following:

1. Sign in to AWS Management Console and open your broker's RabbitMQ web console\.

1.  On the RabbitMQ console, on the **Overview** page, choose the name of a node from the **Nodes** list\. 

1.  On the node detail page, choose **Memory details** to expand the section to view the node's memory usage information\. 

The memory usage information that RabbitMQ provides in the web console can help you determine which resources might be consuming too much memory and contributing to the high memory alarm\. For more information about the memory usage details available via the RabbitMQ web console, see [Reasoning About Memory Use ](https://www.rabbitmq.com/memory-use.html) on the RabbitMQ Server Documentation website\.

## Diagnosing high memory alarm using Amazon MQ metrics<a name="diagnose-using-metrics"></a>

Amazon MQ enables metrics for your broker by default\. You can [view your broker metrics](amazon-mq-accessing-metrics.md) by accessing the CloudWatch console, or by using the CloudWatch API\. The following metrics are useful when diagnosing the RabbitMQ high memory alarm\.


| Amazon MQ CloudWatch metric | Reason for high memory use | 
| --- | --- | 
| MessageCount | Messages are stored in memory until they are consumed or discarded\. A high message count might indicate overutilization of resources and can lead to a high memory alarm\. | 
| QueueCount | Queues are stored in memory, and a high number of queues can lead to a high memory alarm\. | 
| ConnectionCount | Client connections utilize memory, and too many simultaneous connections can lead to a high memory alarm\. | 
| ChannelCount | Similar to connections, channels established using each connection are also stored in node memory, and a high number of channels can lead to a high memory alarm\. | 
| ConsumerCount | For every consumer connected to the broker, a set number of messages are loaded from storage into memory before they are delivered to the consumer\. A large number of consumer connections might cause high memory usage and lead to a high memory alarm\. | 
| PublishRate | Publishing messages utilizes the broker' memory\. If the rate at which messages are published to the broker is too high and significantly outpaces the rate at which the broker delivers messages to consumers, the broker might raise a high memory alarm\.  | 

## Addressing high memory alarm<a name="addressing-high-memory-alarm"></a>

For each contributor that you identify, we recommended the following set of actions to mitigate and resolve the broker's high memory alarm\.


| Reason for high memory use | Amazon MQ recommendation | 
| --- | --- | 
| The number of messages in the queues is too high\. |  Do any of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/troubleshooting-action-required-codes-rabbitmq-memory-alarm.html)  | 
| The number of queues configured on the broker is too high\. | Reduce the number of queues\. | 
| The number of connections established on the broker is too high\. | Reduce the number of connections\. For more information, see [Reducing the number of connections and channels](#reducing-connections-and-channels)\. | 
| The number of channels established on the broker is too high\. | Reduce the number of channels\. For more information see, [Reducing the number of connections and channels](#reducing-connections-and-channels)\. | 
| The number of consumers connected to the broker is too high\. | Reduce the number of consumers connected to the broker\. | 
| The message publishing rate is too high\. | Reduce the rate at which publishers send messages to the broker\. | 
| The client connection attempt rate is too high\. | Reduce the frequency at which clients attempt to connect to the broker in order to publish or consume messages, or configure the broker\. | 

## Reducing the number of connections and channels<a name="reducing-connections-and-channels"></a>

Connections to your Amazon MQ for RabbitMQ broker can be closed either by your client applications, or by manually closing them using the RabbitMQ web console\. To close a connection using the RabbitMQ web console do the following\.

1. Sign in to AWS Management Console and open your broker's RabbitMQ web console\.

1.  On the RabbitMQ console, choose the **Connections** tab\. 

1. On the **Connections** page, under **All connections**, choose the name of the connection you want to close from the list\.

1.  On the connection details page, choose **Close this connection** to expand the section, then choose **Force Close**\. Optionally, you can replace the default text for **Reason** with your own description\. Amazon MQ for RabbitMQ will return the reason you specify to the client when you close the connection\. 

1.  Choose **OK** on the dialog box to confirm and close the connection\. 

 When you close a connection, any channels associated with closed connection will also be closed\. 

**Note**  
 Your client applications may be configured to automatically re\-establish connections to the broker after they are closed\. In this case, closing connections from the broker web console will not be sufficient for reducing connection or channel counts\. 

For brokers without public access, you can temporarily block connections by denying inbound traffic on the appropriate message protocol port, for example, port `5671` for AMQP connections\. You can block the port in the security group that you provided to Amazon MQ when creating the broker\. For more information on modifying your security group, see [Adding rules to a security group](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#adding-security-group-rules) in the *Amazon VPC User Guide*\. 

## Addressing paused queue synchronizations in cluster deployments<a name="addressing-paused-queue-sync"></a>

While addressing RabbitMQ's high memory alarms, you may find that messages on one or multiple queues cannot be consumed\. These queues may be in the process of synchronizing messages between nodes, during which the respective queues become unavailable for publishing and consuming\. Queue synchronizations might become paused due to the high memory alarm, and even contribute to the memory alarm\.

For information about stopping and retrying paused queue syncs, see [Resolving RabbitMQ paused queue synchronization](rabbitmq-queue-sync.md)\.

## Addressing restart loops in single\-instance brokers<a name="w279aac33c15b9c25"></a>

An Amazon MQ for RabbitMQ single\-instance broker that raises a high memory alarm is at risk of becoming unavailable if it restarts and does not have enough memory to start up\. This can cause RabbitMQ to enter a restart loop and prevent any further interactions with the broker until the issue is resolved\. If your broker is in a restart loop, you will not be able to apply the Amazon MQ recommended actions previously described in this section to resolve the high memory alarm\.

To recover your broker, we recommend upgrading to a larger instance type with more memory\. Unlike in cluster deployments, you can upgrade a single\-instance broker while it is experiencing a high memory alarm because there are no queue synchronizations to perform between nodes during a restart\.

## Preventing high memory alarms<a name="preventing-high-memory-alarm"></a>

For each contributing factor you identify, we recommends the following set of actions for preventing and reducing the occurrence of RabbitMQ high memory alarms\.


| Reason high memory use | Amazon MQ recommendation | 
| --- | --- | 
| The number of messages in the queues is too high\. |  Do the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/troubleshooting-action-required-codes-rabbitmq-memory-alarm.html)  | 
| The number of queues configured on the broker is too high\. | Set, or reduce the [queue count limit](rabbitmq-defaults.md)\. | 
| The number of connections established on the broker is too high\. | Set, or reduce the [connection count limit](rabbitmq-defaults.md)\. | 
| The number of channels established on the broker is too high\. | Set a maximum number of channels per connection on client applications\. | 
| The number of consumers connected to the broker is too high\. | Set a small consumer [pre\-fetch limit](rabbitmq-defaults.md)\. | 
| The client connection attempt rate is too high\. | Use longer\-lived connections to reduce the number and frequency of connection attempts\. | 

After your broker's memory alarm has been resolved, you can upgrade your host instance type to an instance with additional resources\. For information on how to update your broker's instance type see [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id.html#brokers-broker-id-model-updatebrokerinput](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id.html#brokers-broker-id-model-updatebrokerinput) in the *Amazon MQ REST API Reference*\.

For a complete listing of broker instance types, see [Amazon MQ for RabbitMQ instance types](broker-instance-types.md#rabbitmq-broker-instance-types)\.