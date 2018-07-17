# API Authentication and Authorization for Amazon MQ<a name="amazon-mq-api-authentication-authorization"></a>

Amazon MQ uses standard AWS request signing for API authentication\. For more information, see [Signing AWS API Requests](http://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html) in the *AWS General Reference*\.

**Note**  
Currently, Amazon MQ doesn't support IAM authentication using resource\-based permissions or resource\-based policies\.

To authorize AWS users to work with brokers, configurations, and users, you must edit your IAM policy permissions\.


+ [IAM Permissions Required to Create an Amazon MQ Broker](#amazon-mq-permissions-required-to-create-broker)
+ [Amazon MQ REST API Permissions Reference](#amazon-mq-api-permissions-reference)

## IAM Permissions Required to Create an Amazon MQ Broker<a name="amazon-mq-permissions-required-to-create-broker"></a>

To create a broker, you must either use the `AmazonMQFullAccess` IAM policy or include the following EC2 permissions in your IAM policy\.

The following custom policy is comprised of two statements \(one conditional\) which grant permissions to manipulate the resources which Amazon MQ requires to create an ActiveMQ broker\.

**Important**  
`ec2:CreateNetworkInterface` is required to allow Amazon MQ to create an elastic network interface \(ENI\) in your account on your behalf\.
`ec2:CreateNetworkInterfacePermission` authorizes Amazon MQ to attach the ENI to an ActiveMQ broker\.
`ec2:AuthorizedService` ensures that ENI permissions can be granted only to Amazon MQ service accounts\.

```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Action": [
            "mq:*",
            "[ec2:CreateNetworkInterface](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateNetworkInterface.html)",
            "[ec2:DeleteNetworkInterface](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteNetworkInterface.html)",
            "[ec2:DetachNetworkInterface](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DetachNetworkInterface.html)",
            "[ec2:DescribeInternetGateways](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeInternetGateways.html)",
            "[ec2:DescribeNetworkInterfaces](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeNetworkInterfaces.html)",
            "[ec2:DescribeRouteTables](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeRouteTables.html)",
            "[ec2:DescribeSecurityGroups](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeSecurityGroups.html)",
            "[ec2:DescribeSubnets](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeSubnets.html)",
            "[ec2:DescribeVpcs](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeVpcs.html)"
        ],
        "Effect": "Allow",
        "Resource": "*"
    },{
        "Action": [
            "[ec2:CreateNetworkInterfacePermission](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateNetworkInterfacePermission.html)",
            "[ec2:DeleteNetworkInterfacePermission](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteNetworkInterfacePermission.html)",
            "[ec2:DescribeNetworkInterfacePermission](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeNetworkInterfacePermissions.html)"
        ],
        "Effect": "Allow",
        "Resource": "*",
        "Condition": {
            "StringEquals": {
                "[ec2:AuthorizedService](http://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonec2.html#amazonec2-ec2_AuthorizedService)": "mq.amazonaws.com"
            }
        }
    }]
}
```

For more information, see [Step 2: Create an IAM User and Get Your AWS Credentials](amazon-mq-setting-up.md#create-iam-user) and [Never Modify or Delete the Amazon MQ Elastic Network Interface](connecting-to-amazon-mq.md#never-modify-delete-elastic-network-interface)\.

## Amazon MQ REST API Permissions Reference<a name="amazon-mq-api-permissions-reference"></a>

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