# Plugins<a name="rabbitmq-basic-elements-plugins"></a>

Amazon MQ provides support for the [RabbitMQ management plugin](https://www.rabbitmq.com/management.html) which powers the management API and the RabbitMQ web console\. You can use the web console and the management API to create and manage broker users as well as broker policies\.To learn about how Amazon MQ automatically manages your broker policy to ensure high availability for cluster deployments, see [Cluster deployment for high availability](rabbitmq-broker-architecture-cluster.md)\.

 Amazon MQ also supports the shovel, and federation plugins, which you can configured to move messages from queues and exchanges in one broker to queues and exchanges in other broker instances\.

**Topics**
+ [Shovel plugin](#rabbitmq-shovel-plugin)
+ [Federation plugin](#rabbitmq-federation-plugin)

## Shovel plugin<a name="rabbitmq-shovel-plugin"></a>

Amazon MQ managed brokers support [RabbitMQ shovel](https://www.rabbitmq.com/shovel.html), allowing you to move messages from queues or exchanges to other queues or exchanges in other brokers\. You can use shovels to connect loosely coupled brokers and distribute messages from brokers with heavier message loads to other brokers that may use different users altogether

[Dynamic shovels](https://www.rabbitmq.com/shovel-dynamic.html) are configured using runtime parameters, and therefore can be started and stopeed at any time\. For more information about using dynamic shovels, see [Configuring dynamic shovels](https://www.rabbitmq.com/shovel-dynamic.html) in the RabbitMQ documentation\.

**Note**  
Amazon MQ does not support using static shovels\.

## Federation plugin<a name="rabbitmq-federation-plugin"></a>

 Amazon MQ supports federated exchanges and queues\. You can use federation to connect loosely coupled brokers on different broker instances and replicate the flow of messages between queues, exchages and consumers\.

For example, one of your application's RabbitMQ servers can connect to another server and consume a message from one of its exchanges without creating multiple connections\. In this case, the first server becomes a *downstream* and the second the *upstream*\. Configuring and enabling federattion is done only on downstream brokers\. You can enable federation by using the RabbitMQ web console or the management API\.

For example, using the managment API, you can configure federation by doing the following\.
+ Configure one or more upstreams that define federation connections to other nodes\. You can define federation connections by using the RabbitMQ web console or the management API\. Using the management API, you can create a `POST` requrest to `/api/parameters/federation-upstream/%2f/my-upstream` with the following request body\.

  ```
  {"value":{"uri":"amqp://server-name","expires":3600000}}
  ```
+ Configure a policy to enable your queues or exchanges to become federated\. You can configure policies by usng the RabbitMQ web console, or the management API\. Using the management API, you can create a `POST` requrest to `/api/policies/%2f/federate-me` with the following request body\.

  ```
  {"pattern":"^amq\.", "definition":{"federation-upstream-set":"all"}, "apply-to":"exchanges"}
  ```
**Note**  
The request body assumes exchanges on the server are named beginning with `amq`\. Using regular expression `^amq\.` will ensure that federation is enabled for all exchanges whose names begin with "amq\." The exchanges on your RabbitMQ server can be named differently\.

For more information, see [RabbitMQ Federation Plugin](https://www.rabbitmq.com/federation.html) documentation\.