# Concurrent Store and Dispatch for Queues<a name="concurrent-store-and-dispatch-for-queues"></a>

By default, producers **\(1\)** send messages to the Amazon MQ thread pool **\(2\)**, and consumers receive messages from the thread pool **\(3\)** and then acknowledge the receipt\. However, in cases when your consumer are slower than your producers, or if the combined number of your consumers is insufficient, messages are sent into storage **\(4\)**\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-configuration-concurrent-store-and-dispatch-queues-flag-enabled.png)

By setting the `concurrentStoreAndDispatchQueues` attribute to `false`, you allow messages **\(1\)** to be directed to **\(2\)** and retrieved from **\(3\)** storage\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-configuration-concurrent-store-and-dispatch-queues-flag-disabled.png)

For an example configuration, see [`concurrentStoreAndDispatchQueues`](child-element-details.md#concurrentStoreAndDispatchQueues)\.