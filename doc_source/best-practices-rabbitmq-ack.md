# Acknowledgement and confirmation<a name="best-practices-rabbitmq-ack"></a>

 When a client application sends confirmation of delivery and consumption of messages back to the broker, it is known as *consumer acknowledgment*\. Similairly, the process of sending confirmation to a publisher is known as *publisher confirm*\. Both acknowledgement and confirmation are essential to ensuring data safety when working with RabbitMQ brokers\.

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
 Unacknowledged messages must be cached in memory\. You can limit the number of messages that a consumer pre\-fetches by configuring [pre\-fetch](best-practices-rabbitmq-prefetch.md) settings for a client application\.