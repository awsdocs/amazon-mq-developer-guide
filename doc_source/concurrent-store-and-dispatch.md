# Concurrent Store and Dispatch<a name="concurrent-store-and-dispatch"></a>

By default, producers **\(1\)** send messages to the Amazon MQ thread pool **\(2\)**, and consumers receive messages from the thread pool **\(3\)** and then acknowledge the receipt\. However, in cases when your consumers are slower than your producers' rates, or if the combined number of your consumers is insufficient, messages are sent into storage **\(4\)**\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-configuration-concurrent-store-and-dispatch-queues-flag-enabled.png)

By setting the `concurrentStoreAndDispatchQueues` attribute to `false`, you allow messages **\(1\)** to be directed to **\(2\)** and retrieved from **\(3\)** storage\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-configuration-concurrent-store-and-dispatch-queues-flag-disabled.png)

The following example configuration uses the `concurrentStoreAndDispatchQueue` attribute\.

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<broker xmlns="http://activemq.apache.org/schema/core">
   <persistenceAdapter>
      <kahaDB concurrentStoreAndDispatchQueues="false"/>
   </persistenceAdapter>
</broker>
```