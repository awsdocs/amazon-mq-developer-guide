# Resolving RabbitMQ paused queue synchronization<a name="rabbitmq-queue-sync"></a>

 In an Amazon MQ for RabbitMQ [cluster deployment](rabbitmq-broker-architecture-cluster.md), messages published to each queue are replicated across all three cluster nodes\. This is referred to as [mirroring](https://www.rabbitmq.com/ha.html), and provides High Availability \(HA\) for RabbitMQ brokers\. Queues in a cluster deployment consist of a *main* replica on one node, and one or more *mirrors*\. Every operation applied to a mirrored queue, including enqueuing messages, is first applied to the main queue and then replicated across its mirrors\. 

 For example, consider a mirrored queue replicated across three nodes: the main node, `main`, and two mirrors, `mirror-1` and `mirror-2`\. If all messages in this mirrored queue are successfully propagated to all mirrors, the queue is synchronized\. If a node, `mirror-1`, becomes unavailable for an interval of time, the queue is still operational and can continue to enqueue messages\. Messages published to `main` while `mirror-1` is unavailable must be then replicated to `mirror-1` in order for the queue to become synchronized\. 

 During maintenance windows, Amazon MQ performs all maintenance work one node at a time to ensure that the broker remains operational\. As a result, queues may need to synchronize as each node resumes operation\. During synchronization, messages that need to be replicated to mirrors are loaded into memory from the corresponding Amazon Elastic Block Store \(EBS\) volume in order to be processed in batches\. Processing messages in batches allows queues to synchronize faster\. 

 If queues are kept short and messages are small, the queue will successfully synchronize and resume operation as expected\. However, if the amount of data in a batch approaches the node's memory limit, the node raises a high memory alarm, pausing the queue sync\. Synchronization will not complete until the number of messages in the batch is reduced, or messages are consumed or deleted\.

 The following steps demonstrate applying a `ha-sync-batch-size` policy and restarting the queue sync to resolve a paused queue synchronization\. 

**Note**  
Reducing the queue synchronization batch\-size may result in a higher number of replication transactions\.

**Topics**
+ [Prerequisites](#rabbitmq-queue-sync-prerequisites)
+ [Step 1: Apply a `ha-sync-batch-size` policy](#rabbitmq-queue-sync-step-1)
+ [Step 2: Restart the queue sync](#rabbitmq-queue-sync-step-2)
+ [Next steps](#rabbitmq-queue-sync-next-steps)
+ [Related resources](#rabbitmq-queue-sync-related-resources)

## Prerequisites<a name="rabbitmq-queue-sync-prerequisites"></a>

 In order to perform the steps in this tutorial, you need an Amazon MQ for RabbitMQ broker user with administrator permissions\. You can use the administrator user created when the broker was first created, or another user that you may have created afterwards\. The following table provides the necessary user tag and permissions\. 


| Tags | Read RegExp | Configure RegExp | Write RegExp | 
| --- | --- | --- | --- | 
| administrator | \.\* | \.\* | \.\* | 

 To learn more about creating RabbitMQ users and managing user tags and permissions, see [User](rabbitmq-basic-elements-user.md)\. 

## Step 1: Apply a `ha-sync-batch-size` policy<a name="rabbitmq-queue-sync-step-1"></a>

 The following procedure demonstrates adding a policy that will apply to all queues created on the broker\. 

**To apply a `ha-sync-batch-size` policy using the RabbitMQ web console**

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. In the navigation pane on the left, choose **Brokers**\.

1. From the list of brokers, choose the name of the broker to which you want to apply the new policy\.

1. On the broker details page, scroll down to the **Connections** section and select the URL listed under **RabbitMQ web console** to open the RabbitMQ web console in a new window\.

1. Sign\-in to the RabbitMQ web console with your broker user name and password\.

1. In the RabbitMQ web console, select **Admin** from the navigation bar on the top of the page\.

1. On the **Admin** page, from the navigation pane on the right, select **Policies**\.

1. On the **Policies** page, you will be able to see a list of the broker’s current policies\. Below the list of current policies, select **Add / update policy** to open the policy form\.
**Note**  
Amazon MQ for RabbitMQ clusters are created, by default, with an initial broker policy named `ha-all-AWS-OWNED-DO-NOT-DELETE`\. This policy is managed by Amazon MQ to ensure that every queue on the broker is replicated to all three nodes and that queues are synchronized automatically\.

1. For the **Name** input field, add a name for your policy, for example, `batch-size-policy`\.

1. For the **Pattern** input field, add the following RegExp pattern: `.*` so that the policy is a matches for all queues on the broker\.

1. For **Apply to**, select **Exchanges and queues** from the available options\.

1. For the **Priority** input field, add an integer bigger than zero\. Because the managed `ha-all-AWS-OWNED-DO-NOT-DELETE` policy is configured by default with priority zero, you must add a higher priority value so that your custom policies can take effect\. For more information about policy priorities, and how to combine policies, see [Policies](https://www.rabbitmq.com/parameters.html#policies) in the RabbitMQ Server Documentation\.

1. For **Definition**, add the following key\-value pairs:
   + `ha-sync-batch-size=100`\. Select **Number** from the available options\.
**Note**  
You may need to adjust and calibrate the batch\-size value based on the number of unsynchronized messages and their size\.
   + `ha-mode=all`\. Select **String** from the availbale options\.
   + `ha-sync-mode=automatic`\. Select **String** from the available options\.
**Important**  
The `ha-mode` definition is required for all HA\-related policies\. Omitting it will result in a validation failure\.
The `ha-sync-mode` definition is required for all custom policies\. Amazon MQ will automatically append this definiton if it is omitted\.

1. Choose **Add / update policy**\.

1. Confirm that the new policy has been added to the list of broker policies displayed\.

**To apply a `ha-sync-batch-size` policy using the RabbitMQ management API**

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. In the navigation pane on the left, choose **Brokers**\.

1. From the list of brokers, choose the name of the broker to which you want to apply the new policy\.

1. On the broker details page, scroll down to the **Connections** section and note the broker URL under **RabbitMQ web console**\. This is the broker endpoint that you will use to create an HTTP request\.

1. Open a new terminal or command line window of your choice\.

1. Use a `curl` command to create a new broker policy\. The following assumes a queue on the default `/` vhost, which is encoded as `%2F`\.

   ```
   curl -i -u username:password -H "content-type:application/json" -XPUT \
   -d '{"pattern":".*", "priority":1, "definition":{"ha-sync-batch-size":100, "ha-mode":"all", "ha-sync-mode":"automatic"}}' \
   https://b-589c045f-f8ln-4ab0-a89c-co62e1c32ef8.mq.us-west-2.amazonaws.com/api/policies/%2F/batch-size-policy
   ```

1. Confirm that the new policy has been added by using a `curl` command to list all broker policies\.

   ```
   curl -i -u username:password https://b-589c045f-f8ln-4ab0-a89c-co62e1c32ef8.mq.us-west-2.amazonaws.com/api/policies
   ```

## Step 2: Restart the queue sync<a name="rabbitmq-queue-sync-step-2"></a>

**To restart the queue sync using the RabbitMQ web console**

1. In the RabbitMQ web console, select **Queues** from the navigation bar on the top of the page\.

1. On the **Queues** page, from the list of queues displayed, locate the paused queue\. Your queue should now list the new policy you created under the **Features** column\.

1. Choose **Restart sync** to restart the synchronization process with a reduced batch\-size\.

**Note**  
If synchronization pauses and does not finish successfully, use a lower `ha-sync-batch-size` value and restart the queue sync again\.

## Next steps<a name="rabbitmq-queue-sync-next-steps"></a>
+  Once your queue completes synchronizing successfully, you can monitor the amount of memory used by your RabbitMQ brokers by accessing the `RabbitMQMemUsed` CloudWatch metric\. You can use `RabbitMQMemLimit` to monitor a node's memory limit\. To learn more about accessing CloudWatch metrics for your Amazon MQ brokers, see [Accessing CloudWatch metrics for Amazon MQ](amazon-mq-accessing-metrics.md)\. To learn more about other CloudWatch metrics you can use to monitor your Amazon MQ for RabbitMQ brokers, see [Logging and monitoring RabbitMQ brokers](security-logging-monitoring-cloudwatch.md#rabbitmq-logging-monitoring)\. 
+ In order to prevent paused queue synchronization, we recommend keeping queues short and processing messages\. For workloads with larger message sizes, we also recommend upgrading your broker instance type to a larger instance\-size with more memory\. To learn more about broker instance type options, see [RabbitMQ instance types](broker-instance-types.md#rabbitmq-broker-instance-types)\. To learn more about editing broker preferences and changing your broker’s instance type, see [Editing broker preferences](amazon-mq-rabbitmq-editing-broker-preferences.md)\. 

## Related resources<a name="rabbitmq-queue-sync-related-resources"></a>
+  [ Updating broker instance type using the Amazon MQ API ](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id.html#brokers-broker-id-model-updatebrokerinput) 
+ [Classic mirroring guide in the RabbitMQ Server Documentation](https://www.rabbitmq.com/ha.html)
+ [Policies and parameters guide in the RabbitMQ Server Documentation](https://www.rabbitmq.com/parameters.html#policies)
+ [RabbitMQ management API documentation](https://pulse.mozilla.org/api/)