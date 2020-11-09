# Pre\-fetching<a name="best-practices-rabbitmq-prefetch"></a>

 You can use the RabbitMQ pre\-fetch value to optimize how your consumers consume messages\. RabbitMQ implements the channel pre\-fetch mechanism provided by AMQP 0\-9\-1 by applying the pre\-fetch count to consumers as opposed to channels\. The pre\-fetch value is used to specify how many messages are being sent to the consumer at any given time\. By default, RabbitMQ sets an unlimited buffer size for client applications\. 

 There are a variety of factors to consider when setting a pre\-fetch count for your RabbitMQ consumers\. First, consider your consumers' environment and configuration\. Because consumers need to keep all messages in memory as they are being processed, a high pre\-fetch value can have a negative impact on your consumers' performance, and in some cases, can result in a consumer potentially crashing all together\. Similairly, the RabbitMQ broker itself keeps all messages that it sends cached in memory until it recieves consumer acknowledgement\. A high pre\-fetch value can cause your RabbitMQ server to run out of memory quickly if automatic acknowledgement is not configured for consumers, and if consumers take a relatively long time to process messages\. 

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