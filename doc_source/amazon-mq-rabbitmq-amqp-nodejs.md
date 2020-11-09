# Using the AMQP client library for Node\.js<a name="amazon-mq-rabbitmq-amqp-nodejs"></a>

 This tutorial describes using `amqplib.node`, the [AMQP 0\-9\-1 library and client for Node\.js](http://www.squaremobius.net/amqp.node/), to create two simple scripts\. The first script configures a *producer* that sends a message\. The second script configures a *reciever* that recieves messages and prints them out\. 

**Topics**
+ [Prerequisites](#amazon-mq-rabbitmq-amqp-nodejs-prerequisites)
+ [Step 1: send a message](#amazon-mq-rabbitmq-amqp-nodejs-step-1-send)
+ [Step 2: recieve a message](#amazon-mq-rabbitmq-amqp-nodejs-step-2-recieve)
+ [Step 3: run the scripts](#amazon-mq-rabbitmq-amqp-nodejs-step-3-run)
+ [Related resources](#amazon-mq-rabbitmq-amqp-nodejs-related-resources)

## Prerequisites<a name="amazon-mq-rabbitmq-amqp-nodejs-prerequisites"></a>

1. Install the latest stable release of [Node\.js](https://nodejs.org/en/) for your operating system of choice\.

1. Use [npm](https://www.npmjs.com/) to install the `amqplib.node` library as shown by the following\.

   ```
   npm install amqplib
   ```

## Step 1: send a message<a name="amazon-mq-rabbitmq-amqp-nodejs-step-1-send"></a>

1. Create a file named `send.js` that will be used to create and configure a producer\. The producer will connect to RabbitMQ, send a message, and then exit\.

1. In `send.js`, require the `amqp.node` library at the top of the file\.

   ```
   const amqp = require('amqplib/callback_api')
   ```

1. Connect to the RabbitMQ server using the broker's secure AMQP URL and create a connection channel\. To learn more about finding your RabbitMQ broker's AMQP URL using the AWS Management Console, see [Listing brokers and viewing broker details](amazon-mq-listing-brokers.md)\.

   ```
   amqp.connect('amqps://b-764397a6-e097-4h6s-86e4-ds77a5885869.mq.us-west-2.amazonaws.com:5671', function(error0, connection) {
     if (error0) {
       throw error0;
     }
     connection.createChannel(function(error1, channel) {});
   });
   ```

1. Declare a queue and then publish a message to the declared queue as shown in the following\.

   ```
   amqp.connect('amqps://b-764397a6-e097-4h6s-86e4-ds77a5885869.mq.us-west-2.amazonaws.com:5671', function(error0, connection) {
     if (error0) {
       throw error0;
     }
     connection.createChannel(function(error1, channel) {
       if (error1) {
         throw error1;
       }
       let queue = 'testQueue';
       let msg = 'Hello world';
   
       channel.assertQueue(queue, {
         durable: false
       });
   
       channel.sendToQueue(queue, Buffer.from(msg));
       console.log(" [x] Sent %s", msg);
     })
   });
   ```
**Note**  
A queue will only be created if another queue with the same name doesn't already exist\.

1. Close the connection and exit\.

   ```
   setTimeout(function() { 
     connection.close(); 
     process.exit(0) 
     }, 500);
   ```

## Step 2: recieve a message<a name="amazon-mq-rabbitmq-amqp-nodejs-step-2-recieve"></a>

1. Create a file named `recieve.js` that will be used to create and configure a reciever\. The reciever will connect to RabbitMQ, and consume messages as they are sent\.
**Note**  
Unlike the producer which publishes a single message, the consumer will continue running to listen for messages and print them out\.

1. In `recieve.js`, require the `amqp.node` library at the top of the file\.

   ```
   const amqp = require('amqplib/callback_api')
   ```

1. Similair to the steps in `send.js`, open a connection and a channel, and declare the queue from which messages are going to be consumed\.

   ```
   amqp.connect('amqps://b-764397a6-e097-4h6s-86e4-ds77a5885869.mq.us-west-2.amazonaws.com:5671', function(error0, connection) {
     if (error0) {
       throw error0;
     }
     connection.createChannel(function(error1, channel) {
       if (error1) {
         throw error1;
       }
       let queue = 'testQueue';
   
       channel.assertQueue(queue, {
         durable: false
       });
     });
   });
   ```
**Note**  
The queue is declared here, as well as in `send.js` because the reciever script might start before the producer\. Declaring the queue in `recieve.js` will make sure the queue exists before you try to consume messages from it\.

1. Because the producer will send messages asynchronously, you must provide a callback that will be executed when RabbitMQ pushes messages to your consumer\. In the following example, `Channel.consume` provides this callback\.

   ```
   console.log(" [*] Waiting for messages in %s. To exit press CTRL+C", queue);
   channel.consume(queue, function(msg) {
     console.log(" [x] Received %s", msg.content.toString());
   }, {
       noAck: true
   });
   ```

## Step 3: run the scripts<a name="amazon-mq-rabbitmq-amqp-nodejs-step-3-run"></a>

Now that you have created both scripts, `send.js` and `recieve.js`, you can run both of them using the terminal\.

1. Navigate to the directory where you created the scripts and then run `send.js` as shown below\.

   ```
   ./send.js
   ```

1. Run `recieve.js` as shown below\.

   ```
   ./recieve.js
   ```

## Related resources<a name="amazon-mq-rabbitmq-amqp-nodejs-related-resources"></a>
+ [`amqplib` API Reference](http://www.squaremobius.net/amqp.node/channel_api.html)
+ [`amqplib` on GitHub](https://github.com/squaremo/amqp.node)