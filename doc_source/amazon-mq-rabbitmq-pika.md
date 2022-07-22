# Using Python Pika with Amazon MQ for RabbitMQ<a name="amazon-mq-rabbitmq-pika"></a>

 The following tutorial shows how you can set up a [Python Pika](https://github.com/pika/pika) client with TLS configured to connect to an Amazon MQ for RabbitMQ broker\. Pika is a Python implementation of the AMQP 0\-9\-1 protocol for RabbitMQ\. This tutorial guides you through installing Pika, declaring a queue, setting up a publisher to send messages to the broker's default exchange, and setting up a consumer to recieve messages from the queue\. 

**Topics**
+ [Prerequisites](#amazon-mq-rabbitmq-pika-prerequisites)
+ [Permissions](#amazon-mq-rabbitmq-pika-permissions)
+ [Step one: Create a basic Python Pika client](#amazon-mq-rabbitmq-pika-basic-client)
+ [Step two: Create a publisher and send a message](#amazon-mq-rabbitmq-pika-publisher-basic-publish)
+ [Step three: Create a consumer and recieve a message](#amazon-mq-rabbitmq-pika-consumer-basic-get)
+ [Step four: \(Optional\) Set up an event loop and consume messages](#amazon-mq-rabbitmq-pika-consumer-basic-consume)
+ [What's next?](#amazon-mq-rabbitmq-pika-whats-next)

## Prerequisites<a name="amazon-mq-rabbitmq-pika-prerequisites"></a>

 To complete the steps in this tutorial, you need the following prerequisites: 
+ An Amazon MQ for RabbitMQ broker\. For more information, see [Creating an Amazon MQ for RabbitMQ broker](getting-started-rabbitmq.md#create-rabbitmq-broker)\.
+ [Python 3](https://www.python.org/downloads/) installed for your operating system\.
+ [Pika](https://pika.readthedocs.io/en/stable/) installed using Python `pip`\. To install Pika, open a new terminal window and run the following\.

  ```
  $ python3 -m pip install pika
  ```

## Permissions<a name="amazon-mq-rabbitmq-pika-permissions"></a>

For this tutorial, you need at least one Amazon MQ for RabbitMQ broker user with permission to write to, and read from, a vhost\. The following table describes the neccessary minimum permissions as regular expression \(regexp\) patterns\.


| Tags | Configure regexp | Write regexp | Read regexp | 
| --- | --- | --- | --- | 
| none |  | \.\* | \.\* | 

 The user permissions listed provide only read and write permissions to the user, without granting access to the management plugin to perform administrative operations on the broker\. You can further restrict permissions by providing regexp patterns that limit the user's access to specified queues\. For example, if you change the read regexp pattern to `^[hello world].*`, the user will only have permission to read from queues that start with `hello world`\. 

For more information about creating RabbitMQ users and managing user tags and permissions, see [User](rabbitmq-basic-elements-user.md)\.

## Step one: Create a basic Python Pika client<a name="amazon-mq-rabbitmq-pika-basic-client"></a>

To create a Python Pika client base class that defines a constructor and provides the SSL context necessary for TLS configuration when interacting with an Amazon MQ for RabbitMQ broker, do the following\.

1.  Open a new terminal window, create a new directory for your project, and navigate to the directory\. 

   ```
   $ mkdir pika-tutorial
   $ cd pika-tutorial
   ```

1.  Create a new file, `basicClient.py`, that contains the following Python code\. 

   ```
   import ssl
   import pika
   
   class BasicPikaClient:
   
       def __init__(self, rabbitmq_broker_id, rabbitmq_user, rabbitmq_password, region):
   
           # SSL Context for TLS configuration of Amazon MQ for RabbitMQ
           ssl_context = ssl.SSLContext(ssl.PROTOCOL_TLSv1_2)
           ssl_context.set_ciphers('ECDHE+AESGCM:!ECDSA')
   
           url = "amqps://{rabbitmq_user}:{rabbitmq_password}@{rabbitmq_broker_id}.mq.{region}.amazonaws.com:5671"
           parameters = pika.URLParameters(url)
           parameters.ssl_options = pika.SSLOptions(context=ssl_context)
   
           self.connection = pika.BlockingConnection(parameters)
           self.channel = self.connection.channel()
   ```

 You can now define additional classes for your publisher and consumer that inherit from `BasicPikaClient`\. 

## Step two: Create a publisher and send a message<a name="amazon-mq-rabbitmq-pika-publisher-basic-publish"></a>

 To create a publisher that declares a queue, and sends a single message, do the following\. 

1.  Copy the contents of the following code sample, and save locally as `publisher.py` in the same directory you created in the previous step\. 

   ```
   from basicClient import BasicPikaClient
   
   class BasicMessageSender(BasicPikaClient):
   
       def declare_queue(self, queue_name):
           print("Trying to declare queue({queue_name})...")
           self.channel.queue_declare(queue=queue_name)
   
       def send_message(self, exchange, routing_key, body):
           channel = self.connection.channel()
           channel.basic_publish(exchange=exchange,
                                 routing_key=routing_key,
                                 body=body)
           print("Sent message. Exchange: {exchange}, Routing Key: {routing_key}, Body: {body}")
   
       def close(self):
           self.channel.close()
           self.connection.close()
   
   if __name__ == "__main__":
   
       # Initialize Basic Message Sender which creates a connection
       # and channel for sending messages.
       basic_message_sender = BasicMessageSender(
           "<broker-id>",
           "<username>",
           "<password>",
           "<region>"
       )
   
       # Declare a queue
       basic_message_sender.declare_queue("hello world queue")
   
       # Send a message to the queue.
       basic_message_sender.send_message(exchange="", routing_key="hello world queue", body=b'Hello World!')
   
       # Close connections.
       basic_message_sender.close()
   ```

    The `BasicMessageSender` class inherits from `BasicPikaClient` and implements additional methods for delaring a queue, sending a message to the queue, and closing connections\. The code sample routes a message to the default exchange, with a routing key equal to the name of the queue\. 

1.  Under `if __name__ == "__main__":`, replace the parameters passed to the `BasicMessageSender` constructor statement with the following information\. 
   +  **`<broker-id>`** – The unique ID that Amazon MQ generates for the broker\. You can parse the ID from your broker ARN\. For example, given the following ARN, `arn:aws:mq:us-east-2:123456789012:broker:MyBroker:b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`, the broker ID would be `b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`\. 
   +  **`<username>`** – The username for a broker user with sufficient permissions to write messages to the broker\. 
   +  **`<password>`** – The password for a broker user with sufficient permissions to write messages to the broker\. 
   +  **`<region>`** – The AWS region in which you created your Amazon MQ for RabbitMQ broker\. For example, `us-west-2`\. 

1.  Run the following command in the same directory you created `publisher.py`\. 

   ```
   $ python3 publisher.py
   ```

   If the code runs successfully, you will see the following output in your terminal window\.

   ```
   Trying to declare queue(hello world queue)...
   Sent message. Exchange: , Routing Key: hello world queue, Body: b'Hello World!'
   ```

## Step three: Create a consumer and recieve a message<a name="amazon-mq-rabbitmq-pika-consumer-basic-get"></a>

 To create a consumer that recieves a single message from the queue, do the following\. 

1.  Copy the contents of the following code sample, and save locally as `consumer.py` in the same directory\. 

   ```
   from basicClient import BasicPikaClient
   
   class BasicMessageReceiver(BasicPikaClient):
   
       def get_message(self, queue):
           method_frame, header_frame, body = self.channel.basic_get(queue)
           if method_frame:
               print(method_frame, header_frame, body)
               self.channel.basic_ack(method_frame.delivery_tag)
               return method_frame, header_frame, body
           else:
               print('No message returned')
   
       def close(self):
           self.channel.close()
           self.connection.close()
   
   
   if __name__ == "__main__":
   
       # Create Basic Message Receiver which creates a connection
       # and channel for consuming messages.
       basic_message_receiver = BasicMessageReceiver(
           "<broker-id>",
           "<username>",
           "<password>",
           "<region>"
       )
   
       # Consume the message that was sent.
       basic_message_receiver.get_message("hello world queue")
   
       # Close connections.
       basic_message_receiver.close()
   ```

    Similar to the the publisher you created in the previous step, `BasicMessageReciever` inherits from `BasicPikaClient` and implements additional methods for recieving a single message, and closing connections\.

1.  Under the `if __name__ == "__main__":` statement, replace the parameters passed to the `BasicMessageReciever` constructor with your information\. 

1.  Run the following command in your project directory\. 

   ```
   $ python3 consumer.py
   ```

   If the code runs successfully, you will see the message body, and headers including the routing key, displayed in your terminal window\.

   ```
   <Basic.GetOk(['delivery_tag=1', 'exchange=', 'message_count=0', 'redelivered=False', 'routing_key=hello world queue'])> <BasicProperties> b'Hello World!'
   ```

## Step four: \(Optional\) Set up an event loop and consume messages<a name="amazon-mq-rabbitmq-pika-consumer-basic-consume"></a>

 To consume multiple messages from a queue, use Pika's [https://pika.readthedocs.io/en/stable/modules/channel.html#pika.channel.Channel.basic_consume](https://pika.readthedocs.io/en/stable/modules/channel.html#pika.channel.Channel.basic_consume) method and a callback function as shown in the following 

1.  In `consumer.py`, add the following method definition to the `BasicMessageReceiver` class\. 

   ```
   def consume_messages(self, queue):
       def callback(ch, method, properties, body):
               print(" [x] Received %r" % body)
   
           self.channel.basic_consume(queue=queue, on_message_callback=callback, auto_ack=True)
   
           print(' [*] Waiting for messages. To exit press CTRL+C')
           self.channel.start_consuming()
   ```

1.  In `consumer.py`, under `if __name__ == "__main__":`, invoke the `consume_msesages` method you defined in the previous step\. 

   ```
   if __name__ == "__main__":
   
       # Create Basic Message Receiver which creates a connection and channel for consuming messages.
       basic_message_receiver = BasicMessageReceiver(
           "<broker-id>",
           "<username>",
           "<password>",
           "<region>"
       )
   
       # Consume the message that was sent.
       # basic_message_receiver.get_message("hello world queue")
   
       # Consume multiple messages in an event loop.
       basic_message_receiver.consume_messages("hello world queue")
   
       # Close connections.
       basic_message_receiver.close()
   ```

1.  Run `consumer.py` again, and if successful, the queued messages will be displayed in your terminal window\. 

   ```
   [*] Waiting for messages. To exit press CTRL+C
   [x] Received b'Hello World!'
   [x] Received b'Hello World!'
   ...
   ```

## What's next?<a name="amazon-mq-rabbitmq-pika-whats-next"></a>
+  For more information about other supported RabbitMQ client libraries, see [RabbitMQ Client Documentation](https://www.rabbitmq.com/clients.html) on the RabbitMQ website\. 
