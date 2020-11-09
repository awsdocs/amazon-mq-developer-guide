# Broker architecture<a name="rabbitmq-broker-architecture"></a>

RabbitMQ brokers can be created as *single\-instance brokers* or in a *cluster deployment*\. For both deployment modes, Amazon MQ provides high durability by storing its data redundantly\.

You can access your RabbitMQ brokers by using [any programming language that RabbitMQ supports](https://www.rabbitmq.com/devtools.html) and by enabling TLS for the following protocols:
+ [AMQP \(0\-9\-1\)](https://www.rabbitmq.com/specification.html)

**Topics**
+ [Single\-instance broker](rabbitmq-broker-architecture-single-instance.md)
+ [Cluster deployment for high availability](rabbitmq-broker-architecture-cluster.md)