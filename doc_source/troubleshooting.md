# Troubleshooting Amazon MQ<a name="troubleshooting"></a>

 This section describes common issues you might encounter when using Amazon MQ brokers, and the steps you can take to resolve them\. 

**Contents**
+ [Troubleshooting: general](general.md)
  + [I can't connect to my broker web console or endpoints\.](general.md#issues-connecting-to-console-or-endpoint)
  + [My broker is running, and I can verify connectivity using `telnet`, but my clients are unable to connect and are returing SSL exceptions\.](general.md#issues-ssl-certificate-exception)
  + [I created a broker but broker creation failed\.](general.md#issues-creating-a-broker)
  + [My broker restarted and I'm not sure why\.](general.md#w252aac33b7c13)
+ [Troubleshooting: Amazon MQ for ActiveMQ](troubleshooting-activemq.md)
  + [I can't see general or audit logs for my broker in CloudWatch Logs even though I’ve activated logging\.](troubleshooting-activemq.md#issues-cw-logging-activemq)
  + [After broker restart or maintenance window, I can't connect to my broker even though the status is `RUNNING`\. Why?](troubleshooting-activemq.md#issues-connection-after-restart)
  + [I see some of my clients connecting to the broker, while others are unable to connect\.](troubleshooting-activemq.md#issues-connection-limit)
  + [I'm seeing exception `org.apache.jasper.JasperException: An exception occurred processing JSP page` on the ActiveMQ console when performing operations\.](troubleshooting-activemq.md#issues-jsp-exception)
+ [Troubleshooting: Amazon MQ for RabbitMQ](troubleshooting-rabbitmq.md)
  + [I can’t see metrics for my queues or virtual hosts in CloudWatch\.](troubleshooting-rabbitmq.md#issues-cw-metrics-rabbitmq)
  + [How do I enable plugins in Amazon MQ for RabbitMQ?](troubleshooting-rabbitmq.md#issues-enabling-plugins-rabbitmq)
  + [I'm unable to change Amazon VPC configuration for the broker\.](troubleshooting-rabbitmq.md#issues-changing-vpc-configration-rabbitmq)