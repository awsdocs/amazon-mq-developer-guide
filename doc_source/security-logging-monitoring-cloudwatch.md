# Monitoring Amazon MQ brokers using Amazon CloudWatch<a name="security-logging-monitoring-cloudwatch"></a>

Amazon MQ and Amazon CloudWatch are integrated so you can use CloudWatch to view and analyze metrics for your ActiveMQ broker and the broker's destinations \(queues and topics\)\. You can view and analyze your Amazon MQ metrics from the CloudWatch console, the AWS CLI, or the CloudWatch CLI\. CloudWatch metrics for Amazon MQ are automatically polled from the broker and then pushed to CloudWatch every minute\.

For information, see [Accessing CloudWatch metrics for Amazon MQ](amazon-mq-accessing-metrics.md)\.

**Note**  
The following statistics are valid for all of the metrics:  
`Average`
`Minimum`
`Maximum`
`Sum`

The `AWS/AmazonMQ` namespace includes the following metrics\.

**Topics**
+ [Logging and monitoring Amazon MQ for ActiveMQ brokers](#activemq-logging-monitoring)
+ [Logging and monitoring Amazon MQ for RabbitMQ brokers](#rabbitmq-logging-monitoring)

## Logging and monitoring Amazon MQ for ActiveMQ brokers<a name="activemq-logging-monitoring"></a>

### Amazon MQ for ActiveMQ metrics<a name="security-logging-monitoring-cloudwatch-metrics"></a>


| Metric | Unit | Description | 
| --- | --- | --- | 
| AmqpMaximumConnections | Count | The maximum number of clients you can connect to your broker using AMQP\. For more information on connection quotas, see [Quotas in Amazon MQ](amazon-mq-limits.md)\. | 
| BurstBalance | Percent | The percentage of burst credits remaining on the Amazon EBS volume used to persist message data for throughput\-optimized brokers\. If this balance reaches zero, the IOPS provided by the Amazon EBS volume will decrease until the Burst Balance refills\. For more information on how Burst Balances work in Amazon EBS, see: [I/O Credits and Burst Performance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html#IOcredit)\. | 
| CpuCreditBalance | Credits \(vCPU\-minutes\) |   This metric is available only for the `mq.t2.micro` broker instance type\. CPU credit metrics are available only at five\-minute intervals\.  The number of earned CPU credits that an instance has accrued since it was launched or started \(including the number of launch credits\)\. The credit balance is available for the broker instance to spend on bursts beyond the baseline CPU utilization\. Credits are accrued in the credit balance after they're earned and removed from the credit balance after they're spent\. The credit balance has a maximum limit\. Once the limit is reached, any newly earned credits are discarded\.  | 
| CpuUtilization | Percent | The percentage of allocated Amazon EC2 compute units that the broker currently uses\. | 
| CurrentConnectionsCount | Count | The current number of active connections on the current broker\. | 
| EstablishedConnectionsCount | Count | The total number of connections, active and inactive, that have been established on the broker\. | 
| HeapUsage | Percent | The percentage of the ActiveMQ JVM memory limit that the broker currently uses\. | 
| InactiveDurableTopicSubscribersCount | Count | The number of inactive durable topic subscribers, up to a maximum of 2000\.  | 
| JobSchedulerStorePercentUsage | Percent | The percentage of disk space used by the job scheduler store\. | 
| JournalFilesForFastRecovery | Count | The number of journal files that will be replayed after a clean shutdown\. | 
| JournalFilesForFullRecovery | Count | The number of journal files that will be replayed after an unclean shutdown\. | 
| MqttMaximumConnections | Count | The maximum number of clients you can connect to your broker using MQTT\. For more information on connection quotas, see [Quotas in Amazon MQ](amazon-mq-limits.md)\. | 
| NetworkConnectorConnectionCount | Count | The number of nodes connected to the broker in a [network of brokers](network-of-brokers.md) using NetworkConnector\. | 
| NetworkIn | Bytes | The volume of incoming traffic for the broker\. | 
| NetworkOut | Bytes | The volume of outgoing traffic for the broker\. | 
| OpenTransactionCount | Count | The total number of transactions in progress\. | 
| OpenwireMaximumConnections | Count | The maximum number of clients you can connect to your broker using OpenWire\. For more information on connection quotas, see [Quotas in Amazon MQ](amazon-mq-limits.md)\. | 
| StompMaximumConnections | Count | The maximum number of clients you can connect to your broker using STOMP\. For more information on connection quotas, see [Quotas in Amazon MQ](amazon-mq-limits.md)\. | 
| StorePercentUsage | Percent | The percent used by the storage limit\. If this reaches 100, the broker will refuse messages\. | 
| TempPercentUsage | Percent | The percentage of available temporary storage used by non\-persistent messages\.  | 
| TotalConsumerCount | Count | The number of message consumers subscribed to destinations on the current broker\. | 
| TotalMessageCount | Count | The number of messages stored on the broker\. | 
| TotalProducerCount | Count | The number of message producers active on destinations on the current broker\. | 
| VolumeReadOps | Count | The number of read operations performed on the Amazon EBS volume\. | 
| VolumeWriteOps | Count | The number of write operations performed on the Amazon EBS volume\. | 
| WsMaximumConnections | Count | The maximum number of clients you can connect to your broker using WebSocket\. For more information on connection quotas, see [Quotas in Amazon MQ](amazon-mq-limits.md)\. | 

#### Dimensions for ActiveMQ broker metrics<a name="security-logging-monitoring-cloudwatch-dimensions"></a>


| Dimension | Description | 
| --- | --- | 
| Broker |  The name of the broker A single\-instance broker has the suffix \-1\. An active/standby broker for high availability has the suffixes \-1 and \-2 for its redundant pair\.  | 

### ActiveMQ destination \(queue and topic\) metrics<a name="security-logging-monitoring-cloudwatch-destination-metrics"></a>

**Important**  
The following metrics include per\-minute counts for the CloudWatch polling period\.  
`EnqueueCount`
`ExpiredCount`
`DequeueCount`
`DispatchCount`
`InFlightCount`
For example, in a five\-minute [CloudWatch period](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html#CloudWatchPeriods), `EnqueueCount` has five count values, each for a one\-minute portion of the period\. The `Minimum` and `Maximum` statistics provide the lowest and highest per\-minute value during the specified period\.


| Metric | Unit | Description | 
| --- | --- | --- | 
| ConsumerCount | Count | The number of consumers subscribed to the destination\. | 
| EnqueueCount | Count | The number of messages sent to the destination, per minute\. | 
| EnqueueTime | Time \(milliseconds\) | The end\-to\-end latency from when a message arrives at a broker until it is delivered to a consumer\. `EnqueueTime` does not measure the end\-to\-end latency from when a message is sent by a producer until it reaches the broker, nor the latency from when a message is received by a broker until it is acknowledged by the broker\. Rather, `EnqueueTime` is the number of milliseconds from the moment a message is received by the broker until it is successfully delivered to a consumer\.   | 
| ExpiredCount | Count | The number of messages that couldn't be delivered because they expired, per minute\. | 
| DispatchCount | Count | The number of messages sent to consumers, per minute\. | 
| DequeueCount | Count | The number of messages acknowledged by consumers, per minute\. | 
| InFlightCount | Count | The number of messages sent to consumers that have not been acknowledged\. | 
| ReceiveCount | Count | The number of messages that have been received from the remote broker for a duplex network connector\. | 
| MemoryUsage | Percent | The percentage of the memory limit that the destination currently uses\. | 
| ProducerCount | Count | The number of producers for the destination\. | 
| QueueSize | Count | The number of messages in the queue\. This metric applies only to queues\.  | 
| TotalEnqueueCount | Count | The total number of messages that have been sent to the broker\. | 
| TotalDequeueCount | Count | The total number of messages that have been consumed by clients\. | 

**Note**  
`TotalEnqueueCount` and `TotalDequeueCount` metrics include messages for advisory topics\. For more information about advisory topic messages, see the [ActiveMQ documentation](https://activemq.apache.org/advisory-message.html)\.

#### Dimensions for ActiveMQ destination \(queue and topic\) metrics<a name="security-logging-monitoring-cloudwatch-destination-dimensions"></a>


| Dimension | Description | 
| --- | --- | 
| Broker |  The name of the broker\.  A single\-instance broker has the suffix `-1`\. An active/standby broker for high availability has the suffixes `-1` and `-2` for its redundant pair\.   | 
| Topic or Queue | The name of the topic or queue\. | 
| NetworkConnector  | The name of the network connector\. | 

## Logging and monitoring Amazon MQ for RabbitMQ brokers<a name="rabbitmq-logging-monitoring"></a>

### RabbitMQ broker metrics<a name="security-logging-monitoring-cloudwatch-metrics-rabbitmq"></a>


| Metric | Unit | Description | 
| --- | --- | --- | 
| ExchangeCount | Count | The total number of exchanges configured on the broker\. | 
| QueueCount | Count | The total number of queues configured on the broker\. | 
| ConnectionCount | Count | The total number of connections established on the broker\. | 
| ChannelCount | Count | The total number of channels established on the broker\. | 
| ConsumerCount | Count | The total number of consumers connected to the broker\. | 
| MessageCount | Count | The total number of messages in the queues\.  The number produced is the total sum of ready and unackknowledged messages on the broker\.  | 
| MessageReadyCount | Count | The total number of ready messages in the queues\. | 
| MessageUnacknowledgedCount | Count | The total number of unacknowledged messages in the queues\. | 
| PublishRate | Count | The rate at which messages are published to the broker\. The number produced represents the number of messages per second at the time of sampling\.  | 
| ConfirmRate | Count | The rate at which the RabbitMQ server is confirming published messages\. You can compare this metric with PublishRate to better understand how your broker is performing\. The number produced represents the number of messages per second at the time of sampling\. | 
| AckRate | Count | The rate at which messages are being acknowledged by consumers\. The number produced represents the number of messages per second at the time of sampling\. | 
| SystemCpuUtilization | Percent | The percentage of allocated Amazon EC2 compute units that the broker currently uses\. For cluster deployments, this value represents the aggregate of all three RabbitMQ nodes' correspondig metric values\. | 
| RabbitMQMemLimit | Bytes | The RAM limit for a RabbitMQ broker\. For cluster deployments, this value represents the aggregate of all three RabbitMQ nodes' correspondig metric values\. | 
| RabbitMQMemUsed | Bytes | The volume of RAM used by a RabbitMQ broker\. For cluster deployments, this value represents the aggregate of all three RabbitMQ nodes' correspondig metric values\. | 
| RabbitMQDiskFreeLimit | Bytes | The disk limit for a RabbitMQ broker\. For cluster deployments, this value represents the aggregate of all three RabbitMQ nodes' correspondig metric values\. This metric is different per instance size\. For more information about Amazon MQ instance types, see [Amazon MQ for RabbitMQ instance types](broker-instance-types.md#rabbitmq-broker-instance-types)\. | 
| RabbitMQDiskFree | Bytes | The total volume of free disk space available in a RabbitMQ broker\. When disk usage goes above its limit, the cluster will block all producer connections\. For cluster deployments, this value represents the aggregate of all three RabbitMQ nodes' correspondig metric values\. | 
| RabbitMQFdUsed | Count | Number of file descriptors used\. For cluster deployments, this value represents the aggregate of all three RabbitMQ nodes' correspondig metric values\. | 

### Dimensions for RabbitMQ broker metrics<a name="security-logging-monitoring-cloudwatch-dimensions-rabbitmq"></a>


| Dimension | Description | 
| --- | --- | 
| Broker |  The name of the broker\.  | 

### RabbitMQ node metrics<a name="security-logging-monitoring-cloudwatch-destination-metrics-rabbitmq"></a>


| Metric | Unit | Description | 
| --- | --- | --- | 
| SystemCpuUtilization | Percent | The percentage of allocated Amazon EC2 compute units that the broker currently uses\. | 
| RabbitMQMemLimit | Bytes | The RAM limit for a RabbitMQ node\. | 
| RabbitMQMemUsed | Bytes | The volume of RAM used by a RabbitMQ node\. When memory use goes above the limit, the cluster will block all producer connections\. | 
| RabbitMQDiskFreeLimit | Bytes | The disk limit for a RabbitMQ node\. This metric is different per instance size\. For more information about Amazon MQ instance types, see [Amazon MQ for RabbitMQ instance types](broker-instance-types.md#rabbitmq-broker-instance-types)\. | 
| RabbitMQDiskFree | Bytes | The total volume of free disk space available in a RabbitMQ node\. When disk usage goes above its limit, the cluster will block all producer connections\. | 
| RabbitMQFdUsed | Count | Number of file descriptors used\. | 

### Dimensions for RabbitMQ node metrics<a name="security-logging-monitoring-cloudwatch-destination-dimensions-rabbitmq"></a>


| Dimension | Description | 
| --- | --- | 
| Node | The name of the node\.  A node name consists of two parts: a prefix \(usuallly `rabbit`\) and a hostname\. For example, `rabbit@ip-10-0-0-230.us-west-2.compute.internal` is a node name with the prefix `rabbit` and the hostname `ip-10-0-0-230.us-west-2.compute.internal`\.   | 
| Broker |  The name of the broker\.  | 

### RabbitMQ queue metrics<a name="security-logging-monitoring-cloudwatch-queue-metrics-rabbitmq"></a>


| Metric | Unit | Description | 
| --- | --- | --- | 
| ConsumerCount | Count | The number of consumers subscribed to the queue\. | 
| MessageReadyCount | Count | The number of messages that are currently available to be delivered\. | 
| MessageUnacknowledgedCount | Count | The number of messages for which the server is awaiting acknowledgement\. | 
| MessageCount | Count | The total number of MessageReadyCount and MessageUnacknowledgedCount \(also known as queue depth\)\. | 

### Dimensions for RabbitMQ queue metrics<a name="security-logging-monitoring-cloudwatch-dimensions-queue-rabbitmq"></a>

**Note**  
Amazon MQ for RabbitMQ will not publish metrics for virtual hosts and queues with names containing blank spaces, tabs or other non\-ASCII characters\.  
For more information about dimension names, see [Dimension](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_Dimension.html#API_Dimension_Contents) in the *Amazon CloudWatch API Reference*\. 


| Dimension | Description | 
| --- | --- | 
| Queue | The name of the queue\. | 
| VirtualHost | Name of the virtual host\. | 