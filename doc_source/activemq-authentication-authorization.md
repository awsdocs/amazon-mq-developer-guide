# Messaging Authentication and Authorization for ActiveMQ<a name="activemq-authentication-authorization"></a>

You can access your brokers using the following protocols with TLS enabled:

+ [AMQP](http://activemq.apache.org/amqp.html)

+ [MQTT](http://activemq.apache.org/mqtt.html)

+ MQTT over [WebSocket](http://activemq.apache.org/websockets.html)

+ [OpenWire](http://activemq.apache.org/openwire.html)

+ [STOMP](http://activemq.apache.org/stomp.html)

+ STOMP over WebSocket

Amazon MQ uses native ActiveMQ authentication\. For information about restrictions related to ActiveMQ usernames and passwords, see [Users](amazon-mq-limits.md#activemq-user-limits)\.

To authorize ActiveMQ users and groups to works with queues and topics, you must [edit your broker's configuration](amazon-mq-editing-managing-configurations.md)\. For information about configuring Amazon MQ, see [Amazon MQ Broker Configuration Parameters](amazon-mq-broker-configuration-parameters.md)\.

**Note**  
Currently, Amazon MQ doesn't support Client Certificate Authentication or plugins for Java Authentication and Authorization Service \(JAAS\)\.