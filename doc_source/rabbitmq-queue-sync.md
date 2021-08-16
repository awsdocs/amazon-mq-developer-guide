# Resolving RabbitMQ paused queue synchronization<a name="rabbitmq-queue-sync"></a>

In an Amazon MQ for RabbitMQ [cluster deployment](rabbitmq-broker-architecture-cluster.md), messages published to each queue are replicated across three broker nodes\. This replication, referred to as *mirroring*, provides high availability \(HA\) for RabbitMQ brokers\. Queues in a cluster deployment consist of a *main* replica on one node and one or more *mirrors*\. Every operation applied to a mirrored queue, including enqueuing messages, is first applied to the main queue and then replicated across its mirrors\.

For example, consider a mirrored queue replicated across three nodes: the main node \(`main`\) and two mirrors \(`mirror-1` and `mirror-2`\)\. If all messages in this mirrored queue are successfully propagated to all mirrors, then the queue is synchronized\. If a node \(`mirror-1`\) becomes unavailable for an interval of time, the queue is still operational and can continue to enqueue messages\. However, for the queue to synchronize, messages published to `main` while `mirror-1` is unavailable must be replicated to `mirror-1`\.

For more information about mirroring, see [Classic Mirrored Queues](https://www.rabbitmq.com/ha.html) on the RabbitMQ website\.

**Maintenance and queue synchronization**

During [maintenance windows](amazon-mq-rabbitmq-editing-broker-preferences.md#rabbitmq-edit-current-configuration-console), Amazon MQ performs all maintenance work one node at a time to ensure that the broker remains operational\. As a result, queues might need to synchronize as each node resumes operation\. During synchronization, messages that need to be replicated to mirrors are loaded into memory from the corresponding Amazon Elastic Block Store \(Amazon EBS\) volume to be processed in batches\. Processing messages in batches lets queues synchronize faster\.

If queues are kept short and messages are small, the queues successfully synchronize and resume operation as expected\. However, if the amount of data in a batch approaches the node's memory limit, the node raises a high memory alarm, pausing the queue sync\. You can confirm memory usage by comparing the `RabbitMemUsed` and `RabbitMqMemLimit` [broker node metrics in CloudWatch](amazon-mq-accessing-metrics.md)\. Synchronization can't complete until messages are consumed or deleted, or the number of messages in the batch is reduced\.

**Note**  
Reducing the queue synchronization batch size can result in a higher number of replication transactions\.

To resolve a paused queue synchronization, follow the steps in this tutorial, which demonstrates applying an `ha-sync-batch-size` policy and restarting the queue sync\.

**Topics**
+ [Prerequisites](#rabbitmq-queue-sync-prerequisites)
+ [Step 1: Apply an `ha-sync-batch-size` policy](#rabbitmq-queue-sync-step-1)
+ [Step 2: Restart the queue sync](#rabbitmq-queue-sync-step-2)
+ [Next steps](#rabbitmq-queue-sync-next-steps)
+ [Related resources](#rabbitmq-queue-sync-related-resources)

## Prerequisites<a name="rabbitmq-queue-sync-prerequisites"></a>

For this tutorial, you must have an Amazon MQ for RabbitMQ broker user with administrator permissions\. You can use the administrator user created when you first created the broker, or another user that you might have created afterwards\. The following table provides the necessary administrator user tag and permissions as regular expression \(regexp\) patterns\.


| Tags | Read regexp | Configure regexp | Write regexp | 
| --- | --- | --- | --- | 
| administrator | \.\* | \.\* | \.\* | 

For more information about creating RabbitMQ users and managing user tags and permissions, see [User](rabbitmq-basic-elements-user.md)\.

## Step 1: Apply an `ha-sync-batch-size` policy<a name="rabbitmq-queue-sync-step-1"></a>

The following procedures demonstrate adding a policy that applies to all queues created on the broker\. You can use the RabbitMQ web console or the RabbitMQ management API\. For more information, see [Management Plugin](https://www.rabbitmq.com/management.html) on the RabbitMQ website\.

**To apply an `ha-sync-batch-size` policy using the RabbitMQ web console**

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. In the left navigation pane, choose **Brokers**\.

1. From the list of brokers, choose the name of the broker to which you want to apply the new policy\.

1. On the broker's page, in the **Connections** section, choose the **RabbitMQ web console** URL\. The RabbitMQ web console opens in a new browser tab or window\.

1. Log in to the RabbitMQ web console with your broker administrator user name and password\.

1. In the RabbitMQ web console, at the top of the page, choose **Admin**\.

1. On the **Admin** page, in the right navigation pane, choose **Policies**\.

1. On the **Policies** page, you can see a list of the broker's current **User policies**\. Below **User policies**, expand **Add / update a policy**\.
**Note**  
By default, Amazon MQ for RabbitMQ clusters are created with an initial broker policy named `ha-all-AWS-OWNED-DO-NOT-DELETE`\. Amazon MQ manages this policy to ensure that every queue on the broker is replicated to all three nodes and that queues are synchronized automatically\.

1. To create a new broker policy, under **Add / update a policy**, do the following:

   1. For **Name**, enter a name for your policy, for example, **batch\-size\-policy**\.

   1. For **Pattern**, enter the regexp pattern **\.\*** so that the policy matches all queues on the broker\.

   1. For **Apply to**, choose **Exchanges and queues** from the dropdown list\.

   1. For **Priority**, enter an integer greater than all other policies in applied to the vhost\. You can apply exactly one set of policy definitions to RabbitMQ queues and exchanges at any given time\. RabbitMQ chooses the matching policy with the highest priority value\. For more information about policy priorities and how to combine policies, see [Policies](https://www.rabbitmq.com/parameters.html#policies) in the RabbitMQ Server Documentation\.

   1. For **Definition**, add the following key\-value pairs:
      + **ha\-sync\-batch\-size**=*100*\. Choose **Number** from the dropdown list\.
**Note**  
You might need to adjust and calibrate the value of `ha-sync-batch-size` based on the number and size of unsynchronized messages in your queues\.
      + **ha\-mode**=**all**\. Choose **String** from the dropdown list\.
**Important**  
The `ha-mode` definition is required for all HA\-related policies\. Omitting it results in a validation failure\.
      + **ha\-sync\-mode**=**automatic**\. Choose **String** from the dropdown list\.
**Note**  
The `ha-sync-mode` definition is required for all custom policies\. If it is omitted, Amazon MQ automatically appends the definition\.

   1. Choose **Add / update policy**\.

1. Confirm that the new policy appears in the list of **User policies**\.

**To apply an `ha-sync-batch-size` policy using the RabbitMQ management API**

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. In the left navigation pane, choose **Brokers**\.

1. From the list of brokers, choose the name of the broker to which you want to apply the new policy\.

1. On the broker's page, in the **Connections** section, note the **RabbitMQ web console** URL\. This is the broker endpoint that you use in an HTTP request\.

1. Open a new terminal or command line window of your choice\.

1. To create a new broker policy, enter the following `curl` command\. This command assumes a queue on the default `/` vhost, which is encoded as `%2F`\.
**Note**  
Replace *username* and *password* with your broker administrator user name and password\. You might need to adjust and calibrate the value of `ha-sync-batch-size` \(*100*\) based on the number and size of unsynchronized messages in your queues\. Replace the broker endpoint with the URL that you noted previously\.

   ```
   curl -i -u username:password -H "content-type:application/json" -XPUT \
   -d '{"pattern":".*", "priority":1, "definition":{"ha-sync-batch-size":100, "ha-mode":"all", "ha-sync-mode":"automatic"}}' \
   https://b-589c045f-f8ln-4ab0-a89c-co62e1c32ef8.mq.us-west-2.amazonaws.com/api/policies/%2F/batch-size-policy
   ```

1. To confirm that the new policy is added to your broker's user policies, enter the following `curl` command to list all broker policies\.

   ```
   curl -i -u username:password https://b-589c045f-f8ln-4ab0-a89c-co62e1c32ef8.mq.us-west-2.amazonaws.com/api/policies
   ```

## Step 2: Restart the queue sync<a name="rabbitmq-queue-sync-step-2"></a>

After applying a new `ha-sync-batch-size` policy to your broker, restart the queue sync\.

**To restart the queue sync using the RabbitMQ web console**
**Note**  
To open the RabbitMQ web console, see the previous instructions in Step 1 of this tutorial\.

1. In the RabbitMQ web console, at the top of the page, choose **Queues**\.

1. On the **Queues** page, under **All queues**, locate your paused queue\. In the **Features** column, your queue should list the name of the new policy that you created \(for example, `batch-size-policy`\)\.

1. To restart the synchronization process with a reduced batch size, choose **Restart sync**\.

**Note**  
If synchronization pauses and doesn't finish successfully, try reducing the `ha-sync-batch-size` value and restarting the queue sync again\.

## Next steps<a name="rabbitmq-queue-sync-next-steps"></a>
+ Once your queue synchronizes successfully, you can monitor the amount of memory that your RabbitMQ nodes use by viewing the Amazon CloudWatch metric `RabbitMQMemUsed`\. You can also view the `RabbitMQMemLimit` metric to monitor a node's memory limit\. For more information, see [Accessing CloudWatch metrics for Amazon MQ](amazon-mq-accessing-metrics.md) and [Logging and monitoring Amazon MQ for RabbitMQ brokers](security-logging-monitoring-cloudwatch.md#rabbitmq-logging-monitoring)\.
+ To prevent paused queue synchronization, we recommend keeping queues short and processing messages\. For workloads with larger message sizes, we also recommend upgrading your broker instance type to a larger instance size with more memory\. For more information about broker instance types and editing broker preferences, see [Amazon MQ for RabbitMQ instance types](broker-instance-types.md#rabbitmq-broker-instance-types) and [Editing broker preferences](amazon-mq-rabbitmq-editing-broker-preferences.md)\.
+  When you create a new Amazon MQ for RabbitMQ broker, Amazon MQ applies a set of default policies and virtual host limits to optimize broker performance\. If your broker does not have the recommended default policies and limits, we recommend creating them yourself\. For more information about creating default policies and vhost limits, see [Broker defaults](rabbitmq-defaults.md)\. 

## Related resources<a name="rabbitmq-queue-sync-related-resources"></a>
+  [UpdateBrokerInput](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id.html#brokers-broker-id-model-updatebrokerinput) – Use this broker property to update a broker instance type using the Amazon MQ API\.
+ [Parameters and Policies](https://www.rabbitmq.com/parameters.html) \(RabbitMQ Server Documentation\) – Learn more about RabbitMQ parameters and policies on the RabbitMQ website\.
+ [RabbitMQ Management HTTP API](https://pulse.mozilla.org/api/) – Learn more about the RabbitMQ management API\.