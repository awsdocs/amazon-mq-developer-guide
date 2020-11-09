# Configuring RabbitMQ logs<a name="security-logging-monitoring-rabbitmq"></a>

 When you enable CloudWatch logging for your RabbitMQ brokers, Amazon MQ uses a service\-linked role to publish general logs to CloudWatch\. If no Amazon MQ service\-linked role exists when you first create a broker, Amazon MQ will automatically create one\. All subsequent RabbitMQ brokers will use the same service\-linked role to publish logs to CloudWatch\.

For more information about service\-linked roles, see [Using service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the *AWS Identity and Access Management User Guide*\. For more information about how Amazon MQ uses service\-linked roles, see [Using service\-linked roles for Amazon MQ](using-service-linked-roles.md)\. 

**Note**  
Audit logging is not supported for RabbitMQ brokers,