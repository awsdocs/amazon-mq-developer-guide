# Working Examples of Using Java Message Service \(JMS\) with ActiveMQ<a name="amazon-mq-working-java-example"></a>

The following examples show how you can work with ActiveMQ programmatically:

+ The OpenWire example Java code connects to a broker, creates a queue, and sends and receives a message\. For a detailed breakdown and explanation, see [Tutorial: Connecting a Java Application to Your Amazon MQ Broker](amazon-mq-connecting-application.md)\.

+ The MQTT example Java code connects to a broker, creates a topic, and publishes and receives a message\.

## Prerequisites<a name="quick-start-prerequisites"></a>

### Enable Inbound Connections<a name="quick-start-allow-inbound-connections"></a>

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. From the broker list, choose the name of your broker \(for example, **MyBroker**\)\.

1. On the ***MyBroker*** page, in the **Connections** section, note the addresses and ports of the broker's ActiveMQ Web Console URL and wire\-level protocols\.

1. In the **Details** section, under **Security and network**, choose the name of your security group or ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/images/amazon-mq-tutorials-broker-details-link.png)\.

   The **Security Groups** page of the EC2 Dashboard is displayed\.

1. From the security group list, choose your security group\.

1. At the bottom of the page, choose **Inbound**, and then choose **Edit**\.

1. In the **Edit inbound rules** dialog box, add a rule for every URL or endpoint that you want to be publicly accessible \(the following example shows how to do this for an ActiveMQ Web Console\)\.

   1. Choose **Add Rule**\.

   1. For **Type**, select **Custom TCP**\.

   1. For **Port Range**, type the ActiveMQ Web Console port \(`8162`\)\.

   1. For **Source**, leave **Custom** selected and then type the IP address of the system that you want to be able to access the ActiveMQ Web Console \(for example, `192.0.2.1`\)\.

   1. Choose **Save**\.

      Your broker can now accept inbound connections\.

### Add Java Dependencies<a name="quick-start-java-dependencies"></a>

------
#### [ OpenWire ]

Ensure that the `activemq-client.jar` and `activemq-pool.jar` packages are in your Java build class path\.

The following example shows these dependencies in a Maven project `pom.xml` file\.

```
<dependencies>
    <dependency>
        <groupId>org.apache.activemq</groupId>
        <artifactId>activemq-client</artifactId>
        <version>5.15.0</version>
    </dependency>
    <dependency>
        <groupId>org.apache.activemq</groupId>
        <artifactId>activemq-pool</artifactId>
        <version>5.15.0</version>
    </dependency>
</dependencies>
```

For more information about `activemq-client.jar`, see [Initial Configuration](http://activemq.apache.org/initial-configuration.html) in the Apache ActiveMQ documentation\.

------
#### [ MQTT ]

Ensure that the `org.eclipse.paho.client.mqttv3.jar` package is in your Java build class path\.

The following example shows this dependency in a Maven project `pom.xml` file\.

```
<dependencies>
    <dependency>
        <groupId>org.eclipse.paho</groupId>
        <artifactId>org.eclipse.paho.client.mqttv3</artifactId>
        <version>1.2.0</version>
    </dependency>                               
</dependencies>
```

For more information about `org.eclipse.paho.client.mqttv3.jar`, see [Eclipse Paho Java Client](https://www.eclipse.org/paho/clients/java/)\.

------

## AmazonMQExample\.java<a name="working-java-example"></a>

------
#### [ OpenWire ]

```
/*
 * Copyright 2010-2018 Amazon.com, Inc. or its affiliates. All Rights Reserved.
 *
 * Licensed under the Apache License, Version 2.0 (the "License").
 * You may not use this file except in compliance with the License.
 * A copy of the License is located at
 *
 *  https://aws.amazon.com/apache2.0
 *
 * or in the "license" file accompanying this file. This file is distributed
 * on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either
 * express or implied. See the License for the specific language governing
 * permissions and limitations under the License.
 *
 */

import org.apache.activemq.ActiveMQConnectionFactory;
import org.apache.activemq.jms.pool.PooledConnectionFactory;

import javax.jms.*;

public class AmazonMQExample {

    public static void main(String[] args) throws JMSException {
        // Specify the connection parameters.
        final String wireLevelEndpoint = "ssl://b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.mq.us-east-2.amazonaws.com:61617";
        final String activeMqUsername = "MyUsername123";
        final String activeMqPassword = "MyPassword456";

        // Create a connection factory.
        final ActiveMQConnectionFactory connectionFactory = new ActiveMQConnectionFactory(wireLevelEndpoint);

        // Pass the username and password.
        connectionFactory.setUserName(activeMqUsername);
        connectionFactory.setPassword(activeMqPassword);

        // Create a pooled connection factory.
        final PooledConnectionFactory pooledConnectionFactory = new PooledConnectionFactory();
        pooledConnectionFactory.setConnectionFactory(connectionFactory);
        pooledConnectionFactory.setMaxConnections(10);

        // Establish a connection for the producer.
        final Connection producerConnection = pooledConnectionFactory.createConnection();
        producerConnection.start();

        // Create a session.
        final Session producerSession = producerConnection.createSession(false, Session.AUTO_ACKNOWLEDGE);

        // Create a queue named "MyQueue".
        final Destination producerDestination = producerSession.createQueue("MyQueue");

        // Create a producer from the session to the queue.
        final MessageProducer producer = producerSession.createProducer(producerDestination);
        producer.setDeliveryMode(DeliveryMode.NON_PERSISTENT);

        // Create a message.
        final String text = "Hello from Amazon MQ!";
        final TextMessage producerMessage = producerSession.createTextMessage(text);

        // Send the message.
        producer.send(producerMessage);
        System.out.println("Message sent.");

        // Clean up the producer.
        producer.close();
        producerSession.close();
        producerConnection.close();

        // Establish a connection for the consumer.
        // Note: Consumers should not use PooledConnectionFactory.
        final Connection consumerConnection = connectionFactory.createConnection();
        consumerConnection.start();

        // Create a session.
        final Session consumerSession = consumerConnection.createSession(false, Session.AUTO_ACKNOWLEDGE);

        // Create a queue named "MyQueue".
        final Destination consumerDestination = consumerSession.createQueue("MyQueue");

        // Create a message consumer from the session to the queue.
        final MessageConsumer consumer = consumerSession.createConsumer(consumerDestination);

        // Begin to wait for messages.
        final Message consumerMessage = consumer.receive(1000);

        // Receive the message when it arrives.
        final TextMessage consumerTextMessage = (TextMessage) consumerMessage;
        System.out.println("Message received: " + consumerTextMessage.getText());

        // Clean up the consumer.
        consumer.close();
        consumerSession.close();
        consumerConnection.close();
        pooledConnectionFactory.stop();
    }
}
```

------
#### [ MQTT ]

```
/*
 * Copyright 2010-2018 Amazon.com, Inc. or its affiliates. All Rights Reserved.
 *
 * Licensed under the Apache License, Version 2.0 (the "License").
 * You may not use this file except in compliance with the License.
 * A copy of the License is located at
 *
 *  https://aws.amazon.com/apache2.0
 *
 * or in the "license" file accompanying this file. This file is distributed
 * on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either
 * express or implied. See the License for the specific language governing
 * permissions and limitations under the License.
 *
 */
                            
import org.eclipse.paho.client.mqttv3.*;

public class AmazonMQExample implements MqttCallback {

    public static void main(String[] args) throws Exception {
        new AmazonMQExample().run();
    }

    private void run() throws MqttException, InterruptedException {
        // Specify the connection parameters.
        final String wireLevelEndpoint = "ssl://b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9-1.mq.us-east-2.amazonaws.com:61617";
        final String activeMqUsername = "MyUsername123";
        final String activeMqPassword = "MyPassword456";

        // Specify the topic name and the message text.
        final String topic = "topic";
        final String text = "Hello from Amazon MQ!";

        // Create the MQTT client and specify the connection options.
        final String clientId = "abc123";
        final MqttClient client = new MqttClient(wireLevelEndpoint, clientId);
        final MqttConnectOptions connOpts = new MqttConnectOptions();

        // Pass the username and password.
        connOpts.setUserName(activeMqUsername);
        connOpts.setPassword(activeMqPassword.toCharArray());

        // Create a session and subscribe to a topic filter.
        client.connect(connOpts);
        client.setCallback(this);
        client.subscribe("+");

        // Create a message.
        final MqttMessage message = new MqttMessage(text.getBytes());

        // Publish the message to a topic.
        client.publish(topic, message);
        System.out.println("Message published: " + text);

        // Wait for the message to be received.
        Thread.sleep(3000L);

        // Clean up the connection.
        client.disconnect();
    }

    @Override
    public void connectionLost(Throwable cause) {
        System.out.println("Connection lost.");
    }

    @Override
    public void messageArrived(String topic, MqttMessage message) throws MqttException {
        System.out.println("Received message from topic " + topic + ": " + message);
    }

    @Override
    public void deliveryComplete(IMqttDeliveryToken token) {
        System.out.println("Delivery complete.");
    }
}
```

------