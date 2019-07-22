# Encryption<a name="amazon-mq-encryption"></a>

User data stored in Amazon MQ is encrypted at rest\. Amazon MQ encryption at rest provides enhanced security by encrypting your data using encryption keys stored in the AWS Key Management Service \(KMS\)\. This service helps reduce the operational burden and complexity involved in protecting sensitive data\. With encryption at rest, you can build security\-sensitive applications that meet encryption compliance and regulatory requirements\.

All connections between Amazon MQ brokers use Transport layer Security \(TLS\) to provide encryption in transit\. 

Amazon MQ encrypts messages at rest and in transit using encryption keys that it manages and stores securely\. For additional security, we highly recommend designing your application to use client\-side encryption\. For more information, see the *[AWS Encryption SDK Developer Guide](https://docs.aws.amazon.com/encryption-sdk/latest/developer-guide/)*\.

## Encryption at Rest<a name="encryption-at-rest"></a>

Amazon MQ integrates with AWS Key Management Service \(KMS\) to offer transparent server\-side encryption\. Amazon MQ always encrypts your data at rest\. When you create a broker, you can specify the AWS KMS customer master key \(CMK\) that you want Amazon MQ to use to encrypt your data at rest\. If you don't specify a CMK, Amazon MQ creates an AWS managed CMK for you and uses it on your behalf\. For more information about CMKs, see [Customer Master Keys \(CMKs\)]() in the AWS Key Management Service Developer Guide\.

When creating a broker, you can configure what Amazon MQ uses for your encryption key by selecting one of the following\.
+ **AWS owned CMK ** — The key is owned by Amazon MQ and is not in your account\.
+ **AWS managed CMK** — The AWS managed CMK \(aws/mq\) is a CMK in your account that is created, managed, and used on your behalf by Amazon MQ\.
+ **Select existing customer managed CMK** — Customer managed CMKs are created and managed by you in AWS Key Management Service \(KMS\)\.

**Important**  
Amazon MQ uses Amazon Elastic File System \(EFS\) to store message data\. If you revoke the grant that gives Amazon EFS permission to use the KMS keys in your account, Amazon MQ cannot access this data and your broker will stop working\. When you revoke a grant for Amazon EFS, it will not take place immediately\. To revoke access rights, delete your broker rather than revoking the grant\.

## Encryption in Transit<a name="encryption-in-transit"></a>

Amazon MQ encrypts data in transit between the brokers of your Amazon MQ deployment\. All data that passes between Amazon MQ brokers is encrypted using Transport layer Security \(TLS\)\. This is true for all available protocols\. 

You can access your brokers using the following protocols with TLS enabled:
+ [AMQP](http://activemq.apache.org/amqp.html)
+ [MQTT](http://activemq.apache.org/mqtt.html)
+ MQTT over [WebSocket](http://activemq.apache.org/websockets.html)
+ [OpenWire](http://activemq.apache.org/openwire.html)
+ [STOMP](http://activemq.apache.org/stomp.html)
+ STOMP over WebSocket