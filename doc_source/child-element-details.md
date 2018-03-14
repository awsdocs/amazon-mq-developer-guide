# Child Element Attributes<a name="child-element-details"></a>

The following is a detailed explanation of child element attributes\. For more information, see [XML Configuration](http://activemq.apache.org/xml-configuration.html) in the Apache ActiveMQ documentation\.

## authorizationEntry<a name="authorizationEntry"></a>

`authorizationEntry` is a child of the `authorizationEntries` child collection element\.

### admin|read|write<a name="admin-read-write"></a>

**Amazon MQ Default:** Not configured\.

**Example Example Configuration**  

```
<authorizationPlugin>
   <map>
      <authorizationMap>
         <authorizationEntries>
            <authorizationEntry admin="admins,activemq-webconsole" read="admins,users,activemq-webconsole" write="admins,activemq-webconsole" queue=">"/>
            <authorizationEntry admin="admins,activemq-webconsole" read="admins,users,activemq-webconsole" write="admins,activemq-webconsole" topic=">"/>
         </authorizationEntries>
      </authorizationMap>
   </map>
</authorizationPlugin>
```

For more information, see [Always Configure an Authorization Map](using-amazon-mq-securely.md#always-configure-authorization-map)\.

## kahaDB<a name="kahaDB"></a>

`kahaDB` is a child of the `persistenceAdapter` child collection element\.

### concurrentStoreAndDispatchQueues<a name="concurrentStoreAndDispatchQueues"></a>

**Amazon MQ Default:** `true`

**Example Example Configuration**  

```
<persistenceAdapter>
   <kahaDB concurrentStoreAndDispatchQueues="false"/>
</persistenceAdapter>
```

For more information, see [Concurrent Store and Dispatch for Queues](concurrent-store-and-dispatch-for-queues.md)\.