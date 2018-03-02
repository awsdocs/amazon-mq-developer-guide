# Child Element Attributes<a name="child-element-details"></a>

The following is a detailed explanation of child element attributes\. For more information, see [XML Configuration](http://activemq.apache.org/xml-configuration.html) in the Apache ActiveMQ documentation\.

## kahaDB<a name="kahaDB"></a>

### concurrentStoreAndDispatchQueues<a name="concurrentStoreAndDispatchQueues"></a>

**Amazon MQ Default:** `true`

**Example Example Configuration**  

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<broker xmlns="http://activemq.apache.org/schema/core">
   <persistenceAdapter>
      <kahaDB concurrentStoreAndDispatchQueues="false"/>
   </persistenceAdapter>
</broker>
```

For more information, see [Concurrent Store and Dispatch](concurrent-store-and-dispatch.md)\.