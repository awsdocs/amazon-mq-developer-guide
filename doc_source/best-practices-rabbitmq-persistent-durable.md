# Use persistent and durable queues<a name="best-practices-rabbitmq-persistent-durable"></a>

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