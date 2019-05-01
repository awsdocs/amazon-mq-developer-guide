# Monitoring Amazon MQ Brokers Using Amazon CloudWatch<a name="amazon-mq-monitoring-cloudwatch"></a>

Amazon MQ and Amazon CloudWatch are integrated so you can use CloudWatch to view and analyze metrics for your ActiveMQ broker and the broker's destinations \(queues and topics\)\. You can view and analyze your Amazon MQ metrics from the CloudWatch console, the AWS CLI, or the CloudWatch CLI\. CloudWatch metrics for Amazon MQ are automatically polled from the broker and then pushed to CloudWatch every minute\.

For information about accessing Amazon MQ CloudWatch metrics, see [Tutorial: Accessing CloudWatch Metrics for Amazon MQ](amazon-mq-accessing-metrics.md)\.

**Note**  
The following statistics are valid for all of the metrics:  
`Average`
`Minimum`
`Maximum`
`Sum`

The `AWS/AmazonMQ` namespace includes the following metrics\.

## Broker Metrics<a name="broker-metrics"></a>


| Metric | Unit | Description | 
| --- | --- | --- | 
| CpuCreditBalance | Credits \(vCPU\-minutes\) |   This metric is available only for the `mq.t2.micro` broker instance type\. CPU credit metrics are available only at five\-minute intervals\.  The number of earned CPU credits that an instance has accrued since it was launched or started \(including the number of launch credits\)\. The credit balance is available for the broker instance to spend on bursts beyond the baseline CPU utilization\. Credits are accrued in the credit balance after they're earned and removed from the credit balance after they're spent\. The credit balance has a maximum limit\. Once the limit is reached, any newly earned credits are discarded\.  | 
| CpuUtilization | Percent | The percentage of allocated EC2 compute units that the broker currently uses\. | 
| CurrentConnectionsCount | Count | The current number of active connections on the current broker\. | 
| JournalFilesForFastRecovery | Count | The number of journal files that will be replayed after a clean shutdown\. | 
| JournalFilesForFullRecovery | Count | The number of journal files that will be replayed after an unclean shutdown\. | 
| HeapUsage | Percent | The percentage of the ActiveMQ JVM memory limit that the broker currently uses\. | 
| NetworkIn | Bytes | The volume of incoming traffic for the broker\. | 
| NetworkOut | Bytes | The volume of outgoing traffic for the broker\. | 
| OpenTransactionsCount | Count | The total number of transactions in progress\. | 
| StorePercentUsage | Percent | The percent used by the storage limit\. If this reaches 100 the broker will refuse messages\. | 
| TotalConsumerCount | Count | The number of message consumers subscribed to destinations on the current broker\. | 
| TotalMessageCount | Count | The number of messages stored on the broker\. | 
| TotalProducerCount | Count | The number of message producers active on destinations on the current broker\. | 

### Dimension for Broker Metrics<a name="broker-metrics-dimensions"></a>


| Dimension | Description | 
| --- | --- | 
| Broker |  The name of the broker\.  A single\-instance broker has the suffix `-1`\. An active\-standby broker for high availability has the suffixes `-1` and `-2` for its redundant pair\.   | 

## Destination \(Queue and Topic\) Metrics<a name="destination-queue-topic-metrics"></a>

**Important**  
The following metrics include per\-minute counts for the CloudWatch polling period\.  
`EnqueueCount`
`ExpiredCount`
`DequeueCount`
`DispatchCount`
`InFlightCount`
For instance, in a 5 minute [CloudWatch period](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html#CloudWatchPeriods), `EnqueueCount` has 5 count values, each for a 1 minute portion of the period\. The `Minimum` and `Maximum` statistics provide the lowest and highest per\-minute value during the specified period\.


| Metric | Unit | Description | 
| --- | --- | --- | 
| ConsumerCount | Count | The number of consumers subscribed to the destination\. | 
| EnqueueCount | Count | The number of messages sent to the destination, per minute\. | 
| EnqueueTime | Time \(milliseconds\) | The end\-to\-end latency from when a message arrives at a broker until it is delivered to a consumer\. | 
| ExpiredCount | Count | The number of messages that couldn't be delivered because they expired, per minute\. | 
| DispatchCount | Count | The number of messages sent to consumers, per minute\. | 
| DequeueCount | Count | The number of messages acknowledged by consumers, per minute\. | 
| InFlightCount | Count | The number of messages sent to consumers that have not been acknowledged\. | 
| MemoryUsage | Percent | The percentage of the memory limit that the destination currently uses\. | 
| ProducerCount | Count | The number of producers for the destination\. | 
| QueueSize | Count | The number of messages in the queue\. This metric applies only to queues\.  | 

### Dimensions for Destination \(Queue and Topic\) Metrics<a name="destination-queue-topic-dimensions"></a>


| Dimension | Description | 
| --- | --- | 
| Broker |  The name of the broker\.  A single\-instance broker has the suffix `-1`\. An active\-standby broker for high availability has the suffixes `-1` and `-2` for its redundant pair\.   | 
| Topic or Queue | The name of the topic or queue\. | 