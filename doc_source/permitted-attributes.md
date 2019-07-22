# Elements and Their Attributes Permitted in Amazon MQ Configurations<a name="permitted-attributes"></a>

The following is a detailed listing of the elements and their attributes permitted in Amazon MQ configurations\. For more information, see [XML Configuration](http://activemq.apache.org/xml-configuration.html) in the Apache ActiveMQ documentation\.

If you see an expand arrow \(**↗**\) in the upper\-right corner of the table, you can open the table in a new window\. To close the window, choose the close button \(**X**\) in the lower\-right corner\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/permitted-attributes.html)

## Amazon MQ Parent Element Attributes<a name="parent-element-details"></a>

The following is a detailed explanation of parent element attributes\. For more information, see [XML Configuration](http://activemq.apache.org/xml-configuration.html) in the Apache ActiveMQ documentation\.

**Topics**
+ [broker](#broker-element)

### broker<a name="broker-element"></a>

`broker` is a parent collection element\. 

#### Attributes<a name="broker-attributes"></a>

##### networkConnectionStartAsync<a name="networkConnectionStartAsync"></a>

To mitigate network latency and to allow other networks to start in a timely manner, use the `<networkConnectionStartAsync>` tag\. The tag instructs the broker to use an executor to start network connections in parallel, asynchronous to a broker start\.

**Default:** `false`

#### Example Configuration<a name="networkConnectorStartAsync"></a>

```
<broker networkConnectorStartAsync="false"/>
```