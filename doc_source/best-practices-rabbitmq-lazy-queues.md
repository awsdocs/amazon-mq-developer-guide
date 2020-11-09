# Enable lazy queues<a name="best-practices-rabbitmq-lazy-queues"></a>

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