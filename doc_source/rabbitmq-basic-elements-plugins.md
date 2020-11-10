# Plugins<a name="rabbitmq-basic-elements-plugins"></a>

Amazon MQ supports the [RabbitMQ management plugin](https://www.rabbitmq.com/management.html) which powers the management API and the RabbitMQ web console\. You can use the web console and the management API to create and manage broker users and policies\. To learn about how Amazon MQ automatically manages broker policies to ensure high availability for cluster deployments, see [Cluster deployment for high availability](rabbitmq-broker-architecture-cluster.md)\.

 Amazon MQ also supports the shovel, and federation plugins, which you can configure and use to move messages from queues and exchanges in one broker to queues and exchanges in other broker instances\.

**Topics**
+ [Shovel plugin](#rabbitmq-shovel-plugin)
+ [Federation plugin](#rabbitmq-federation-plugin)

## Shovel plugin<a name="rabbitmq-shovel-plugin"></a>

Amazon MQ managed brokers support [RabbitMQ shovel](https://www.rabbitmq.com/shovel.html), allowing you to move messages from queues and exchanges on one broker instance to another\. You can use shovel to connect loosely coupled brokers and distribute messages away from nodes with heavier message loads\.

Amazon MQ managed RabbitMQ brokers support dynamic shovels\. Dynamic shovels are configured using runtime parameters, and can be started and stopped at any time programatically by a client connection\. For example, using the RabbitMQ management API, you can create a `POST` request to the following API endpoint to configure a dynamic shovel\. In the example, `{vhost}` can be replaced by the name of the broker's vhost, and `{name}` replaced by the name of the new dynamic shovel\.

```
/api/parameters/shovel/{vhost}/{name}
```

In the request body, you must specify either a queue or an exchange but not both\. This example below configures a dynamic shovel between a local queue specified in `src-queue` and a remote queue defined in `dest-queue`\. Similairly, you can use `src-exchange` and `dest-exchange` parameters to configure a shovel between two exchanges\.

```
{
  "value": {
    "src-protocol": "amqp091",
    "src-uri":  "amqp://localhost",
    "src-queue":  "source-queue-name",
    "dest-protocol": "amqp091",
    "dest-uri": "amqps://amqps://b-c8352341-ec91-4a78-ad9c-a43f23d325bb.mq.us-west-2.amazonaws.com:5671",
    "dest-queue": "destination-queue-name"
  }
}
```

For more information about using dynamic shovels, see [Configuring dynamic shovels](https://www.rabbitmq.com/shovel-dynamic.html) in the *RabbitMQ Documentation*\.

**Note**  
Amazon MQ does not support using static shovels\.

## Federation plugin<a name="rabbitmq-federation-plugin"></a>

 Amazon MQ supports federated exchanges and queues\. With federation, you can replicate the flow of messages between queues, exchages and consumers on separate brokers\. Federated queues and exchanges use point\-to\-point links to connect to peers in other brokers\. While federated exchcanges, by default, route messages once, federated queues can move messages any number of times as needed by consumers\.

You can use federation to allow a *downstream* broker to consume a message from an exchange or a queue on an *upstream*\. You can enable federation on downstream brokers by using the RabbitMQ web console or the management API\.

For example, using the managment API, you can configure federation by doing the following\.
+ Configure one or more upstreams that define federation connections to other nodes\. You can define federation connections by using the RabbitMQ web console or the management API\. Using the management API, you can create a `POST` requrest to `/api/parameters/federation-upstream/%2f/my-upstream` with the following request body\.

  ```
  {"value":{"uri":"amqp://server-name","expires":3600000}}
  ```
+ Configure a policy to enable your queues or exchanges to become federated\. You can configure policies by using the RabbitMQ web console, or the management API\. Using the management API, you can create a `POST` requrest to `/api/policies/%2f/federate-me` with the following request body\.

  ```
  {"pattern":"^amq\.", "definition":{"federation-upstream-set":"all"}, "apply-to":"exchanges"}
  ```
**Note**  
The request body assumes exchanges on the server are named beginning with `amq`\. Using regular expression `^amq\.` will ensure that federation is enabled for all exchanges whose names begin with "amq\." The exchanges on your RabbitMQ server can be named differently\.

For more information, see [RabbitMQ Federation Plugin](https://www.rabbitmq.com/federation.html) documentation\.