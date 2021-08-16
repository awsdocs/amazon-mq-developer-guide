# Cluster deployment for high availability<a name="rabbitmq-broker-architecture-cluster"></a>

A *cluster deployment* is a logical grouping of three RabbitMQ broker nodes behind a Network Load Balancer, each sharing users, queues, and a distributed state across multiple Availability Zones \(AZ\)\.

In a cluster deployment, Amazon MQ automatically manages broker policies to enable classic mirroring across all nodes, ensuring high availability \(HA\)\. Each mirrored queue consists of one *main* node and one or more *mirrors*\. Each queue has its own main node\. All operations for a given queue are first applied on the queue's main node and then propagated to mirrors\. Amazon MQ creates a default system policy that sets the `ha-mode ` to `all` and `ha-sync-mode` to `automatic`\. This ensures that data is replicated to all nodes in the cluster across different Availability Zones for better durability\.

**Note**  
During a *maintenance window*, all maintenance to a cluster is performed one node at a time, keeping at least two running nodes at all times\. Each time a node is brought down, client connections to that node are severed and need to be re\-established\. You must ensure that your client code is designed to automatically reconnect to your cluster\. For more information about connection recovery, see [Automatically recover from network failures](best-practices-rabbitmq.md#best-practices-rabbitmq-connection-recovery)\.  
Because Amazon MQ sets `ha-sync-mode: automatic`, during a maintenance window, queues will synchronize when each node re\-joins the cluster\. Queue synchronization blocks all other queue operations\. You can mitigate the impact of queue synchronization during maintenance windows by keeping queues short\.

The default policy should not be deleted\. If you do delete this policy, Amazon MQ will be automatically recreate it\. Amazon MQ will also ensure that HA properties are applied to all other policies that you create on a clustered broker\. If you add a policy without the HA properties, Amazon MQ will add them for you\. If you add a policy with different high availability properties, Amazon MQ will replace them\. For more information about classic mirroring, see [Classic mirrored queues](https://www.rabbitmq.com/ha.html)\.

**Important**  
Amazon MQ does not support [quorum queues](https://www.rabbitmq.com/quorum-queues.html)\. Enabling the quorum queue feature flag and creating quorum queues will result in data loss\.

 The following diagram illustrates a RabbitMQ cluster broker deployment with three nodes in three Availability Zones \(AZ\), each with its own Amazon EBS volume and a shared state\. Amazon EBS provides block level storage optimized for low\-latency and high throughput\. 

![\[Illustrates the cluster deployment broker architecture for RabbitMQ brokers.\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-rabbitmq-broker-architecture-cluster-broker.png)