# Concurrent Store and Dispatch for Queues<a name="concurrent-store-and-dispatch-for-queues"></a>

By default, producers ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/number-1-red.png) send messages to the Amazon MQ thread pool ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/number-2-red.png), and consumers receive messages from the thread pool ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/number-3-red.png) and then acknowledge the receipt\. However, in cases when your consumer are slower than your producers, or if the combined number of your consumers is insufficient, messages are sent into storage ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/number-4-red.png)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-configuration-concurrent-store-and-dispatch-queues-flag-enabled.png)

By setting the `concurrentStoreAndDispatchQueues` attribute to `false`, you allow messages ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/number-1-red.png) to be directed to ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/number-2-red.png) and retrieved from storage ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/number-3-red.png)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-configuration-concurrent-store-and-dispatch-queues-flag-disabled.png)

For an example configuration, see [`concurrentStoreAndDispatchQueues`](child-element-details.md#concurrentStoreAndDispatchQueues)\.