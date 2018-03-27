# Ensuring Effective Amazon MQ Performance<a name="ensuring-effective-amazon-mq-performance"></a>

The following design patterns can improve the effectiveness and performance of your Amazon MQ broker\.

## Disable Concurrent Store and Dispatch for Queues with Slow Consumers<a name="disable-concurrent-store-and-dispatch-queues-flag-slow-consumers"></a>

By default, the `concurrentStoreAndDispatch` flag is set to `true`\. If your consumers are slower than your producers, or if the combined number of your consumers is insufficient, set the flag to `false`\.

For a detailed overview, see [Concurrent Store and Dispatch for Queues in Amazon MQ](concurrent-store-and-dispatch-for-queues.md)\. For an example configuration, see [concurrentStoreAndDispatchQueues](child-element-details.md#concurrentStoreAndDispatchQueues)\.