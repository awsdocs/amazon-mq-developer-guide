# Data protection in Amazon MQ<a name="data-protection"></a>

The AWS [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/) applies to data protection in Amazon MQ\. As described in this model, AWS is responsible for protecting the global infrastructure that runs all of the AWS Cloud\. You are responsible for maintaining control over your content that is hosted on this infrastructure\. This content includes the security configuration and management tasks for the AWS services that you use\. For more information about data privacy, see the [Data Privacy FAQ](http://aws.amazon.com/compliance/data-privacy-faq)\. For information about data protection in Europe, see the [AWS Shared Responsibility Model and GDPR](http://aws.amazon.com/blogs/security/the-aws-shared-responsibility-model-and-gdpr/) blog post on the *AWS Security Blog*\.

For data protection purposes, we recommend that you protect AWS account credentials and set up individual user accounts with AWS Identity and Access Management \(IAM\)\. That way each user is given only the permissions necessary to fulfill their job duties\. We also recommend that you secure your data in the following ways:
+ Use multi\-factor authentication \(MFA\) with each account\.
+ Use SSL/TLS to communicate with AWS resources\. We recommend TLS 1\.2 or later\.
+ Set up API and user activity logging with AWS CloudTrail\.
+ Use AWS encryption solutions, along with all default security controls within AWS services\.
+ Use advanced managed security services such as Amazon Macie, which assists in discovering and securing personal data that is stored in Amazon S3\.
+ If you require FIPS 140\-2 validated cryptographic modules when accessing AWS through a command line interface or an API, use a FIPS endpoint\. For more information about the available FIPS endpoints, see [Federal Information Processing Standard \(FIPS\) 140\-2](http://aws.amazon.com/compliance/fips/)\.

We strongly recommend that you never put sensitive identifying information, such as your customers' account numbers, into free\-form fields such as a **Name** field\. This includes when you work with Amazon MQ or other AWS services using the console, API, AWS CLI, or AWS SDKs\. Any data that you enter into Amazon MQ or other services might get picked up for inclusion in diagnostic logs\. When you provide a URL to an external server, don't include credentials information in the URL to validate your request to that server\.

## Encryption<a name="data-protection-encryption"></a>

User data stored in Amazon MQ is encrypted at rest\. Amazon MQ encryption at rest provides enhanced security by encrypting your data using encryption keys stored in the AWS Key Management Service \(KMS\)\. This service helps reduce the operational burden and complexity involved in protecting sensitive data\. With encryption at rest, you can build security\-sensitive applications that meet encryption compliance and regulatory requirements\.

All connections between Amazon MQ brokers use Transport layer Security \(TLS\) to provide encryption in transit\. 

Amazon MQ encrypts messages at rest and in transit using encryption keys that it manages and stores securely\. For more information, see the *[AWS Encryption SDK Developer Guide](https://docs.aws.amazon.com/encryption-sdk/latest/developer-guide/)*\.

## Encryption at rest<a name="data-protection-encryption-at-rest"></a>

Amazon MQ integrates with AWS Key Management Service \(KMS\) to offer transparent server\-side encryption\. Amazon MQ always encrypts your data at rest\.

### Encryption at rest for ActiveMQ brokers<a name="data-protection-encryption-at-rest-activemq"></a>

When you create an Amazon MQ for ActiveMQ broker, you can specify the AWS KMS key that you want Amazon MQ to use to encrypt your data at rest\. If you don't specify a KMS key, Amazon MQ creates an AWS managed KMS key for you and uses it on your behalf\. For more information about KMS keys, see [AWS KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#master_keys) in the AWS Key Management Service Developer Guide\.

When creating a broker, you can configure what Amazon MQ uses for your encryption key by selecting one of the following\.
+ **AWS owned KMS key** — The key is owned by Amazon MQ and is not in your account\.
+ **AWS managed KMS key** — The AWS managed KMS key \(aws/mq\) is a KMS key in your account that is created, managed, and used on your behalf by Amazon MQ\.
+ **Select existing customer managed KMS key** — Customer managed KMS keys are created and managed by you in AWS Key Management Service \(KMS\)\.

**Important**  
Amazon MQ uses Amazon Elastic File System \(EFS\) to store message data\. If you revoke the grant that gives Amazon EFS permission to use the KMS keys in your account, Amazon MQ cannot access this data and your broker will stop working\. When you revoke a grant for Amazon EFS, it will not take place immediately\. To revoke access rights, delete your broker rather than revoking the grant\.

### Encryption at rest for RabbitMQ brokers<a name="data-protection-encryption-at-rest-rabbitmq"></a>

When you create a RabbitMQ brokers, Amazon MQ creates an AWS managed KMS key and uses it on your behalf\. This AWS managed KMS key is owned by Amazon MQ and is not stored in your AWS account\. Currently, Amazon MQ does not support AWS managed KMS keys owned by you and saved in your account, or customer managed KMS keys created and managed by you\.

 For more information about KMS keys, see [AWS KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#master_keys) in the *AWS Key Management Service Developer Guide*\. 

## Encryption in transit<a name="data-protection-encryption-in-transit"></a>

Amazon MQ encrypts data in transit between the brokers of your Amazon MQ deployment\. All data that passes between Amazon MQ brokers is encrypted using Transport layer Security \(TLS\)\. This is true for all available protocols\. 

 By default, Amazon MQ brokers use the recommended TLS 1\.2 to encrypt data\. 

### Amazon MQ for ActiveMQ protocols<a name="activemq-protocol-and-ciphers"></a>

You can access your ActiveMQ brokers using the following protocols with TLS enabled:
+ [AMQP](http://activemq.apache.org/amqp.html)
+ [MQTT](http://activemq.apache.org/mqtt.html)
+ MQTT over [WebSocket](http://activemq.apache.org/websockets.html)
+ [OpenWire](http://activemq.apache.org/openwire.html)
+ [STOMP](http://activemq.apache.org/stomp.html)
+ STOMP over WebSocket

#### Supported TLS Cipher Suites for ActiveMQ<a name="activemq-tls-support"></a>

ActiveMQ on Amazon MQ supports the following cipher suites:
+ TLS\_ECDHE\_RSA\_WITH\_AES\_256\_GCM\_SHA384
+ TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384
+ TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA
+ TLS\_DHE\_RSA\_WITH\_AES\_256\_GCM\_SHA384
+ TLS\_DHE\_RSA\_WITH\_AES\_256\_CBC\_SHA256
+ TLS\_DHE\_RSA\_WITH\_AES\_256\_CBC\_SHA
+ TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384
+ TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA256
+ TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA
+ TLS\_ECDHE\_RSA\_WITH\_AES\_128\_GCM\_SHA256
+ TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256
+ TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA
+ TLS\_DHE\_RSA\_WITH\_AES\_128\_GCM\_SHA256
+ TLS\_DHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256
+ TLS\_DHE\_RSA\_WITH\_AES\_128\_CBC\_SHA
+ TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256
+ TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256
+ TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA

### Amazon MQ for RabbitMQ protocols<a name="rabbitmq-protocol-and-ciphers"></a>

You can access your RabbitMQ brokers using the following protocols with TLS enabled:
+ [AMQP \(0\-9\-1\)](https://www.rabbitmq.com/specification.html)

#### Supported TLS Cipher Suites for RabbitMQ<a name="activemq-tls-support"></a>

RabbitMQ on Amazon MQ supports the following cipher suites:
+ TLS\_ECDHE\_RSA\_WITH\_AES\_256\_GCM\_SHA384
+ TLS\_ECDHE\_RSA\_WITH\_AES\_128\_GCM\_SHA256