# Broker defaults<a name="rabbitmq-defaults"></a>

When you create an Amazon MQ for RabbitMQ broker, Amazon MQ applies a default set of broker policies and vhost limits to optimize your broker's performance\. Amazon MQ applies vhost limits only to the default \(`/`\) vhost\. Amazon MQ will not apply default policies to newly created vhosts\. We recommend keeping these defaults for all new and existing brokers\. However, you can modify, override, or delete these defaults at any time\.

Amazon MQ creates policies and limits based on the instance type and broker deployment mode that you choose when you create your broker\. The default policies are named according to the deployment mode, as follows:
+ **Single\-instance** – `AWS-DEFAULT-POLICY-SINGLE-INSTANCE`
+ **Cluster deployment** – `AWS-DEFAULT-POLICY-CLUSTER-MULTI-AZ`

For [single\-instance brokers](rabbitmq-broker-architecture-single-instance.md), Amazon MQ sets the policy priority value to `0`\. To override the default priority value, you can create your own custom policies with higher priority values\. For [cluster deployments](rabbitmq-broker-architecture-cluster.md), Amazon MQ sets the priority value to `1` for broker defaults\. To create your own custom policy for clusters, assign a priority value greater than `1`\.

**Note**  
In cluster deployments, `ha-mode` and `ha-sync-mode` broker policies are required for classic mirroring and high availability \(HA\)\.  
If you delete the default `AWS-DEFAULT-POLICY-CLUSTER-MULTI-AZ` policy, Amazon MQ uses the `ha-all-AWS-OWNED-DO-NOT-DELETE` policy with a priority value of `0`\. This ensures that the required `ha-mode` and `ha-sync-mode` policies are still in effect\. If you create your own custom policy, Amazon MQ automatically appends `ha-mode` and `ha-sync-mode` to your policy definitions\.

**Topics**
+ [Policy and limit descriptions](#rabbitmq-defaults-descriptions)
+ [Recommended default values](#rabbitmq-defaults-values)
+ [Manually applying default policies and limits](#rabbitmq-defaults-applying-policies)

## Policy and limit descriptions<a name="rabbitmq-defaults-descriptions"></a>

The following list describes the default policies and limits that Amazon MQ applies to a newly created broker\. The values for `max-length`, `max-queues`, and `max-connections` vary based on your broker's instance type and deployment mode\. These values are listed in the [Recommended default values](#rabbitmq-defaults-values) section\.
+ **`queue-mode: lazy`** \(policy\) – Enables lazy queues\. By default, queues keep an in\-memory cache of messages, enabling the broker to deliver messages to consumers as fast as possible\. This can lead to the broker running out of memory and raising a high\-memory alarm\. Lazy queues attempt to move messages to disk as early as is practical\. This means that fewer messages are kept in memory under normal operating conditions\. Using lazy queues, Amazon MQ for RabbitMQ can support much larger messaging loads and longer queues\. Note that for certain use cases, brokers with lazy queues might perform marginally slower\. This is because messages are moved from disk to broker, as opposed to delivering messages from an in\-memory cache\.
**Deployment modes**  
Single\-instance, cluster
+ **`max-length: number-of-messages`** \(policy\) – Sets a limit for the number of messages in a queue\. In cluster deployments, the limit prevents paused queue synchronization in cases such as broker reboots, or following a maintenance window\.
**Deployment modes**  
Cluster
+ **`overflow: reject-publish`** \(policy\) – Enforces queues with a `max-length` policy to reject new messages after the number of messages in the queue reaches the `max-length` value\. To ensure that messages aren't lost if a queue is in an overflow state, client applications that publish messages to the broker must implement [publisher confirms](best-practices-rabbitmq.md#best-practices-rabbitmq-ack)\. For information about implementing publisher confirms, see [Publisher Confirms](https://www.rabbitmq.com/confirms.html#publisher-confirms) on the RabbitMQ website\.
**Deployment modes**  
Cluster
+ **`max-queues: number-of-queues-per-vhost`** \(vhost limit\) – Sets the limit for the number of queues in a broker\. Similar to the `max-length` policy definition, limiting the number of queues in cluster deployments prevents paused queue synchronization following broker reboots or maintenance windows\. Limiting queues also prevents excessive amounts of CPU usage for maintaining queues\.
**Deployment modes**  
Single\-instance, cluster
+ **`max-connections: number-of-connections-per-vhost`** \(vhost limit\) – Sets the limit for the number of client connections to the broker\. Limiting the number of connections according to the recommended values prevents excessive broker memory usage, which could result in the broker raising a high memory alarm and pausing operations\.
**Deployment modes**  
Single\-instance, cluster

## Recommended default values<a name="rabbitmq-defaults-values"></a>

**Note**  
The `max-length` and `max-queue` default limits are tested and evaluated based on an average message size of 5 kB\. If your messages are significantly larger than 5 kB, you will need to adjust and reduce the `max-length` and `max-queue` limits\.

The following table lists the default limit values for a newly created broker\. Amazon MQ applies these values according to the broker's instance type and deployment mode\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/rabbitmq-defaults.html)


| Instance type | Deployment mode | `max-length` | `max-queues` | `max-connections` | 
| --- | --- | --- | --- | --- | 
| t3\.micro | Single\-instance | N/A | 500 | 500 | 
| m5\.large | Single\-instance | N/A | 20,000 | 4,000 | 
| m5\.large | Cluster | 8,000,000 | 10,000 | 15,000 | 
| m5\.xlarge | Single\-instance | N/A | 30,000 | 8,000 | 
| m5\.xlarge | Cluster | 9,000,000 | 10,000 | 20,000 | 
| m5\.2xlarge | Single\-instance | N/A | 60,000 | 15,000 | 
| m5\.2xlarge | Cluster | 10,000,000 | 10,000 | 40,000 | 
| m5\.4xlarge | Single\-instance | N/A | 150,000 | 30,000 | 
| m5\.4xlarge | Cluster | 12,000,000 | 10,000 | 100,000 | 

## Manually applying default policies and limits<a name="rabbitmq-defaults-applying-policies"></a>

 The following section describes applying custom policies and limits with Amazon MQ recommended default values\. If you have deleted the recommended default policies and limits, and want to re\-create them, or you have created additional vhosts and want to apply the default policies and limits to your new vhosts, you can use the following steps\. 

**Important**  
 To perform the following steps, you must have an Amazon MQ for RabbitMQ broker user with administrator permissions\. You can use the administrator user created when you first created the broker, or another user that you might have created afterwards\. The following table provides the necessary administrator user tag and permissions as regular expression \(regexp\) patterns\.   


| Tags | Read regexp | Configure regexp | Write regexp | 
| --- | --- | --- | --- | 
| administrator | \.\* | \.\* | \.\* | 
For more information about creating RabbitMQ users and managing user tags and permissions, see [User](rabbitmq-basic-elements-user.md)\.

**To apply default policies and virtual host limits using the RabbitMQ web console**

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. In the left navigation pane, choose **Brokers**\.

1. From the list of brokers, choose the name of the broker to which you want to apply the new policy\.

1. On the broker details page, in the **Connections** section, choose the **RabbitMQ web console** URL\. The RabbitMQ web console opens in a new browser tab or window\.

1. Log in to the RabbitMQ web console with your broker administrator user name and password\.

1. In the RabbitMQ web console, at the top of the page, choose **Admin**\.

1. On the **Admin** page, in the right navigation pane, choose **Policies**\.

1. On the **Policies** page, you can see a list of the broker's current **User policies**\. Below **User policies**, expand **Add / update a policy**\.

1. To create a new broker policy, under **Add / update a policy**, do the following:

   1. For **Virtual host**, choose the name of the vhost to which you want to attach the policies from the dropdown list\. To choose the default vhost, choose **/**\.
**Note**  
If you have not created additional vhosts, the **Virtual host** option is not shown in the RabbitMQ console, and the policies are applied only to the default vhost\.

   1. For **Name**, enter a name for your policy, for example, **policy\-defaults**\.

   1. For **Pattern**, enter the regexp pattern **\.\*** so that the policy matches all queues on the broker\.

   1. For **Apply to**, choose **Exchanges and queues** from the dropdown list\.

   1. For **Priority**, enter an integer greater than all other policies applied to the vhost\. You can apply exactly one set of policy definitions to RabbitMQ queues and exchanges at any given time\. RabbitMQ chooses the matching policy with the highest priority value\. For more information about policy priorities and how to combine policies, see [Policies](https://www.rabbitmq.com/parameters.html#policies) in the RabbitMQ Server Documentation\.

   1. For **Definition**, add the following key\-value pairs:
      + **queue\-mode**=**lazy**\. Choose **String** from the dropdown list\.
      + **overflow**=**reject\-publish**\. Choose **String** from the dropdown list\.
**Note**  
Does not apply to single\-instance brokers\.
      + **max\-length**=***number\-of\-messages***\. Replace *number\-of\-messages* with the [Amazon MQ recommended value](#rabbitmq-defaults-values) according to the broker's instance size and deployment mode, for example, **8000000** for an `mq.m5.large` cluster\. Choose **Number** from the dropdown list\.
**Note**  
Does not apply to single\-instance brokers\.

   1. Choose **Add / update policy**\.

1. Confirm that the new policy appears in the list of **User policies**\.
**Note**  
For cluster brokers, Amazon MQ automatically applies the `ha-mode: all` and `ha-sync-mode: automatic` policy definitions\.

1. From the right navigation pane, choose **Limits**\.

1. On the **Limits** page, you can see a list of the broker's current **Virtual host limits**\. Below **Virtual host limits**, expand **Set / update a virtual host limit**\.

1. To create a new vhost limit, under **Set / update a virtual host limit**, do the following:

   1. For **Virtual host**, choose the name of the vhost to which you want to attach the policies from the dropdown list\. To choose the default vhost, choose **/**\.

   1. For **Limit**, choose **max\-connections** from the dropdown options\.

   1. For **Value**, enter the [Amazon MQ recommended value](#rabbitmq-defaults-values) according to the broker's instance size and deployment mode, for example, **15000** for an `mq.m5.large` cluster\.

   1. Choose **Set / update limit**\.

   1. Repeat the steps above, and for **Limit**, choose **max\-queues** from the dropdown options\.

1. Confirm that the new limits appear in the list of **Virtual host limits**\.

**To apply default policies and virtual host limits using the RabbitMQ management API**

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. In the left navigation pane, choose **Brokers**\.

1. From the list of brokers, choose the name of the broker to which you want to apply the new policy\.

1. On the broker's page, in the **Connections** section, note the **RabbitMQ web console** URL\. This is the broker endpoint that you use in an HTTP request\.

1. Open a new terminal or command line window of your choice\.

1. To create a new broker policy, enter the following `curl` command\. This command assumes a queue on the default `/` vhost, which is encoded as `%2F`\. To apply the policy to another vhost, replace `%2F` with the vhost's name\.
**Note**  
Replace *username* and *password* with your administrator user name and password\. Replace *number\-of\-messages* with the [Amazon MQ recommended value](#rabbitmq-defaults-values) according to the broker's instance size and deployment mode\. Replace *policy\-name* with a name for your policy\. Replace *broker\-endpoint* with the URL that you noted previously\.

   ```
   curl -i -u username:password -H "content-type:application/json" -XPUT \
   -d '{"pattern":".*", "priority":1, "definition":{"queue-mode":lazy, "overflow":"reject-publish", "max-length":"number-of-messages"}}' \
   broker-endpoint/api/policies/%2F/policy-name
   ```

1. To confirm that the new policy is added to your broker's user policies, enter the following `curl` command to list all broker policies\.

   ```
   curl -i -u username:password broker-endpoint/api/policies
   ```

1. To create a new `max-connections` virtual host limits, enter the following `curl` command\. This command assumes a queue on the default `/` vhost, which is encoded as `%2F`\. To apply the policy to another vhost, replace `%2F` with the vhost's name\.
**Note**  
Replace *username* and *password* with your administrator user name and password\. Replace *max\-connections* with the [Amazon MQ recommended value](#rabbitmq-defaults-values) according to the broker's instance size and deployment mode\. Replace the broker endpoint with the URL that you noted previously\.

   ```
   curl -i -u username:password -H "content-type:application/json" -XPUT \
   -d '{"value":"number-of-connections"}' \
   broker-endpoint/api/vhost-limits/%2F/max-connections
   ```

1. To create a new `max-queues` virtual host limit, repeat the previous step, but modify the curl command as shown in the following\.

   ```
   curl -i -u username:password -H "content-type:application/json" -XPUT \
   -d '{"value":"number-of-queues"}' \
   broker-endpoint/api/vhost-limits/%2F/max-queues
   ```

1. To confirm that the new limits are added to your broker's virtual host limits, enter the following `curl` command to list all broker virtual host limits\.

   ```
   curl -i -u username:password broker-endpoint/api/vhost-limits
   ```