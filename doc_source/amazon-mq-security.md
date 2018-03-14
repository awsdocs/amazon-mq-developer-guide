# Amazon MQ Security<a name="amazon-mq-security"></a>

This section provides information about Amazon MQ and ActiveMQ authentication and authorization\. For information about security best practices, see [Using Amazon MQ Securely](using-amazon-mq-securely.md)\.


+ [API Authentication and Authorization for Amazon MQ](#amazon-mq-api-authentication-authorization)
+ [Messaging Authentication and Authorization for ActiveMQ](#activemq-authentication-authorization)

## API Authentication and Authorization for Amazon MQ<a name="amazon-mq-api-authentication-authorization"></a>

Amazon MQ uses standard AWS request signing for authentication\. For more information, see [Signing AWS API Requests](http://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html) in the *AWS General Reference*\.

**Note**  
Currently, Amazon MQ doesn't support IAM authentication using resource\-based permissions or resource\-based policies\.

To authorize AWS users to work with brokers, configurations, and users, you must edit your IAM policy permissions\.

**Important**  
To create a broker, you must either use the `AmazonMQFullAccess` IAM policy or include the following EC2 permissions in your IAM policy\.  
`ec2:CreateNetworkInterface`
`ec2:CreateNetworkInterfacePermission`
`ec2:DeleteNetworkInterface`
`ec2:DeleteNetworkInterfacePermission`
`ec2:DetachNetworkInterface`
`ec2:DescribeInternetGateways`
`ec2:DescribeNetworkInterfaces`
`ec2:DescribeNetworkInterfacePermissions`
`ec2:DescribeRouteTables`
`ec2:DescribeSecurityGroups`
`ec2:DescribeSubnets`
`ec2:DescribeVpcs`
For more information, see [Step 2: Create an IAM User and Get Your AWS Credentials](amazon-mq-setting-up.md#create-iam-user) and [Never Modify or Delete the Amazon MQ Elastic Network Interface](communicating-with-amazon-mq.md#never-modify-delete-elastic-network-interface)\.

The following table lists Amazon MQ REST APIs and the corresponding IAM permissions\.


**Amazon MQ REST APIs and Required Permissions**  

| Amazon MQ REST APIs | Required Permissions | 
| --- | --- | 
| [http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-brokers.html#rest-api-brokers-methods-post](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-brokers.html#rest-api-brokers-methods-post) | mq:CreateBroker | 
| [http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configurations.html#rest-api-configurations-methods-post](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configurations.html#rest-api-configurations-methods-post) | mq:CreateConfiguration | 
| [http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-user.html#rest-api-user-methods-post](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-user.html#rest-api-user-methods-post) | mq:CreateUser | 
| [http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker.html#rest-api-broker-methods-delete](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker.html#rest-api-broker-methods-delete) | mq:DeleteBroker | 
| [http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-user.html#rest-api-user-methods-delete](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-user.html#rest-api-user-methods-delete) | mq:DeleteUser | 
| [http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker.html#rest-api-broker-methods-get](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker.html#rest-api-broker-methods-get) | mq:DescribeBroker | 
| [http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration.html#rest-api-configuration-methods-get](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration.html#rest-api-configuration-methods-get) | mq:DescribeConfiguration | 
| [http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration-revision.html#rest-api-configuration-revision-methods-get](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration-revision.html#rest-api-configuration-revision-methods-get) | mq:DescribeConfigurationRevision | 
| [http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-user.html#rest-api-user-methods-get](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-user.html#rest-api-user-methods-get) | mq:DescribeUser | 
| [http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-brokers.html#rest-api-brokers-methods-get](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-brokers.html#rest-api-brokers-methods-get) | mq:ListBrokers | 
| [http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration-revisions.html#rest-api-configuration-revisions-methods-get](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration-revisions.html#rest-api-configuration-revisions-methods-get) | mq:ListConfigurationRevisions | 
| [http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configurations.html#rest-api-configurations-methods-get](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configurations.html#rest-api-configurations-methods-get) | mq:ListConfigurations | 
| [http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-users.html#rest-api-users-methods-get](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-users.html#rest-api-users-methods-get) | mq:ListUsers | 
| [http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker-reboot.html#rest-api-broker-reboot-methods-post](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker-reboot.html#rest-api-broker-reboot-methods-post) | mq:RebootBroker  | 
| [http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker.html#rest-api-broker-methods-put](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker.html#rest-api-broker-methods-put) | mq:UpdateBroker | 
| [http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration.html#rest-api-configuration-methods-put](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration.html#rest-api-configuration-methods-put) | mq:UpdateConfiguration | 
| [http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-user.html#rest-api-user-methods-put](http://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-user.html#rest-api-user-methods-put) | mq:UpdateUser | 

## Messaging Authentication and Authorization for ActiveMQ<a name="activemq-authentication-authorization"></a>

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