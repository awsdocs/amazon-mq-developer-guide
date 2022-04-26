# Amazon MQ for RabbitMQ best practices<a name="best-practices-rabbitmq"></a>

Use this as a reference to quickly find recommendations for maximizing performance and minimizing throughput costs when working with RabbitMQ brokers on Amazon MQ\.

**Topics**
+ [Enable lazy queues](#best-practices-rabbitmq-lazy-queues)
+ [Use persistent and durable queues](#best-practices-rabbitmq-persistent-durable)
+ [Keep queues short](#best-practices-rabbitmq-keep-queues-short)
+ [Configure acknowledgement and confirmation](#best-practices-rabbitmq-ack)
+ [Configure pre\-fetching](#best-practices-rabbitmq-prefetch)
+ [Configure Celery](#best-practices-rabbitmq-celery)
+ [Automatically recover from network failures](#best-practices-rabbitmq-connection-recovery)

## Enable lazy queues<a name="best-practices-rabbitmq-lazy-queues"></a>

 If you are working with very long queues that process large volumes of messages, enabling *lazy queues* can improve your broker's overall performance\.

 RabbitMQ's default behavior is to cache messages in memory and to move them to disk only when the broker needs more available memory\. This process of moving messages from memory to disk can take time and stops the queue from processing messages\. Enabling lazy queues can have a significant impact on speeding up the process of moving messages to disk as lazy queues store messages to disk as soon as possible, resulting in fewer messages cached in memory\.

You can enable lazy queues by setting the `queue.declare` arguments at the time of declaration, or by configuring a policy via the RabbitMQ management console\. The following example demonstrates declaring a lazy queue using the RabbitMQ Java client library\.

```
Map<String, Object> args = new HashMap<String, Object>();
args.put("x-queue-mode", "lazy");
channel.queueDeclare("myqueue", false, false, false, args);
```

**Note**  
Enabling lazy queues can increase disk I/O operations\.

## Use persistent and durable queues<a name="best-practices-rabbitmq-persistent-durable"></a>

 Persistent messages can help prevent data loss in situations where a broker crashes or restarts\. Persistent messages are written to disk as soon as they arrive\. Unlike lazy queues, however, persistent messages are cached both in memory and in disk unless more memory is needed by the broker\. In cases where more memory is needed, messages are removed from memory by the RabbitMQ broker mechanism that manages storing messages to disk, commonly referred to as the *persistence layer*\. 

To enable message persistence, you can declare your queues as `durable` and set message delivery mode to `persistent`\. The following example demonstrates using the [RabbitMQ Java client library](https://www.rabbitmq.com/java-client.html) to declare a durable queue\. 

```
boolean durable = true;
channel.queueDeclare("my_queue", durable, false, false, null);
```

 Once you have configured your queue as durable, you can send a persistent message to your queue by setting `MessageProperties` to `PERSISTENT_TEXT_PLAIN` as shown in the following example\. 

```
import com.rabbitmq.client.MessageProperties;

channel.basicPublish("", "my_queue",
            MessageProperties.PERSISTENT_TEXT_PLAIN,
            message.getBytes());
```

## Keep queues short<a name="best-practices-rabbitmq-keep-queues-short"></a>

In cluster deployments, queues with a large number of messages can lead to resource overutilization\. When a broker is overutilized, rebooting an Amazon MQ for RabbitMQ broker can cause further degradation of performance\. If rebooted, overutilized brokers might become unresponsive in the `REBOOT_IN_PROGRESS` state\.

During [maintenance windows](amazon-mq-rabbitmq-editing-broker-preferences.md#rabbitmq-edit-current-configuration-console), Amazon MQ performs all maintenance work one node at a time to ensure that the broker remains operational\. As a result, queues might need to synchronize as each node resumes operation\. During synchronization, messages that need to be replicated to mirrors are loaded into memory from the corresponding Amazon Elastic Block Store \(Amazon EBS\) volume to be processed in batches\. Processing messages in batches lets queues synchronize faster\.

If queues are kept short and messages are small, the queues successfully synchronize and resume operation as expected\. However, if the amount of data in a batch approaches the node's memory limit, the node raises a high memory alarm, pausing the queue sync\. You can confirm memory usage by comparing the `RabbitMemUsed` and `RabbitMqMemLimit` [broker node metrics in CloudWatch](amazon-mq-accessing-metrics.md)\. Synchronization can't complete until messages are consumed or deleted, or the number of messages in the batch is reduced\.

 If queue synchronization is paused for a cluster deployment, we recommend consuming or deleting messages to lower the number of messages in queues\. Once queue depth is reduced and queue sync completes, the broker status will change to `RUNNING`\. To resolve a paused queue sync, you can also apply a policy to [reduce the queue synchronization batch\-size](rabbitmq-queue-sync.md)\. 

**Warning**  
Do not reboot a broker that is running high on resources\.  
If you reboot a broker when queue synchronization is paused, the broker will reinitiate the synchronization process, which can further degrade broker resources as messages are transferred from storage to node memory, and result in the broker becoming unresponsive in the `REBOOT_IN_PROGRESS` state\. 

## Configure acknowledgement and confirmation<a name="best-practices-rabbitmq-ack"></a>

 When a client application sends confirmation of delivery and consumption of messages back to the broker, it is known as *consumer acknowledgment*\. Similarly, the process of sending confirmation to a publisher is known as *publisher confirm*\. Both acknowledgement and confirmation are essential to ensuring data safety when working with RabbitMQ brokers\.

Consumer delivery acknowledgement is typically configured on the client application\. When working with AMQP 0\-9\-1, acknowledgement can be enabled by configuring the `basic.consume` or when a message is fetched using the `basic.code` method\.

 Typically, delivery acknowledgement is enabled in a channel\. For example, when working with the RabbitMQ Java client library, you can use the `Channel#basicAck` to set up a simple `basic.ack` positive acknowledgement as shown in the following example\. 

```
// this example assumes an existing channel instance

boolean autoAck = false;
channel.basicConsume(queueName, autoAck, "a-consumer-tag",
     new DefaultConsumer(channel) {
         @Override
         public void handleDelivery(String consumerTag,
                                    Envelope envelope,
                                    AMQP.BasicProperties properties,
                                    byte[] body)
             throws IOException
         {
             long deliveryTag = envelope.getDeliveryTag();
             // positively acknowledge a single delivery, the message will
             // be discarded
             channel.basicAck(deliveryTag, false);
         }
     });
```

**Note**  
 Unacknowledged messages must be cached in memory\. You can limit the number of messages that a consumer pre\-fetches by configuring [pre\-fetch](#best-practices-rabbitmq-prefetch) settings for a client application\.

## Configure pre\-fetching<a name="best-practices-rabbitmq-prefetch"></a>

 You can use the RabbitMQ pre\-fetch value to optimize how your consumers consume messages\. RabbitMQ implements the channel pre\-fetch mechanism provided by AMQP 0\-9\-1 by applying the pre\-fetch count to consumers as opposed to channels\. The pre\-fetch value is used to specify how many messages are being sent to the consumer at any given time\. By default, RabbitMQ sets an unlimited buffer size for client applications\. 

 There are a variety of factors to consider when setting a pre\-fetch count for your RabbitMQ consumers\. First, consider your consumers' environment and configuration\. Because consumers need to keep all messages in memory as they are being processed, a high pre\-fetch value can have a negative impact on your consumers' performance, and in some cases, can result in a consumer potentially crashing all together\. Similarly, the RabbitMQ broker itself keeps all messages that it sends cached in memory until it recieves consumer acknowledgement\. A high pre\-fetch value can cause your RabbitMQ server to run out of memory quickly if automatic acknowledgement is not configured for consumers, and if consumers take a relatively long time to process messages\. 

With the above considerations in mind, we recommend always setting a pre\-fetch value in order to prevent situations where a RabbitMQ broker or its consumers run out of memory due to a large number number of unprocessed, or unacknowledged messages\. If you need to optimize your brokers to process large volumes of messages, you can test your brokers and consumers using a range of pre\-fetch counts to determine the value at which point network overhead becomes largely insignificant compared to the time it takes a consumer to process messages\.

**Note**  
If your client applications have configured to automatically acknowledge delivery of messages to consumers, setting a pre\-fetch value will have no effect\.
All pre\-fetched messages are removed from the queue\.

The following example desmonstrate setting a pre\-fetch value of `10` for a single consumer using the RabbitMQ Java client library\.

```
ConnectionFactory factory = new ConnectionFactory();

Connection connection = factory.newConnection();
Channel channel = connection.createChannel();

channel.basicQos(10, false);

QueueingConsumer consumer = new QueueingConsumer(channel);
channel.basicConsume("my_queue", false, consumer);
```

**Note**  
In the RabbitMQ Java client library, the default value for the `global` flag is set to `false`, so the above example can be written simply as `channel.basicQos(10)`\.

## Configure Celery<a name="best-practices-rabbitmq-celery"></a>

 Python Celery sends a lot of unnecessary messages that can make finding and processing the useful information harder\. To reduce the noise and make processing easier, enter the following command: 

```
celery -A app_name worker --without-heartbeat --without-gossip --without-mingle
```

## Automatically recover from network failures<a name="best-practices-rabbitmq-connection-recovery"></a>

We recommend always enabling automatic network recovery to prevent significant downtime in cases where client connections to RabbitMQ nodes fail\. The RabbitMQ Java client library supports automatic network recovery by default, beginning with version `4.0.0`\.

Automatic connection recovery is triggered if an unhandled exception is thrown in the connection's I/O loop, if a socket read operation timeout is detected, or if the server misses a [heartbeat](https://www.rabbitmq.com/heartbeats.html)\.

In cases where the initial connection between a client and a RabbitMQ node fails, automatic recovery will not be triggered\. We recommend writing your application code to account for initial connection failures by retrying the connection\. The following example demonstrates retrying initial network failures using the RabbitMQ Java client library\.

```
ConnectionFactory factory = new ConnectionFactory();
// enable automatic recovery if using RabbitMQ Java client library prior to version 4.0.0.
factory.setAutomaticRecoveryEnabled(true);
// configure various connection settings

try {
  Connection conn = factory.newConnection();
} catch (java.net.ConnectException e) {
  Thread.sleep(5000);
  // apply retry logic
}
```

**Note**  
If an application closes a connection by using the `Connection.Close` method, automatic network recovery will not be enabled or triggered\.