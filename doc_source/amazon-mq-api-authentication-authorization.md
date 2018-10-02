# API Authentication and Authorization for Amazon MQ<a name="amazon-mq-api-authentication-authorization"></a>

Amazon MQ uses standard AWS request signing for API authentication\. For more information, see [Signing AWS API Requests](https://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html) in the *AWS General Reference*\.

**Note**  
Currently, Amazon MQ doesn't support IAM authentication using resource\-based permissions or resource\-based policies\.

To authorize AWS users to work with brokers, configurations, and users, you must edit your IAM policy permissions\.

**Topics**
+ [IAM Permissions Required to Create an Amazon MQ Broker](#amazon-mq-permissions-required-to-create-broker)
+ [Amazon MQ REST API Permissions Reference](#amazon-mq-api-permissions-reference)

## IAM Permissions Required to Create an Amazon MQ Broker<a name="amazon-mq-permissions-required-to-create-broker"></a>

To create a broker, you must either use the `AmazonMQFullAccess` IAM policy or include the following EC2 permissions in your IAM policy\.

The following custom policy is comprised of two statements \(one conditional\) which grant permissions to manipulate the resources which Amazon MQ requires to create an ActiveMQ broker\.

**Important**  
The `ec2:CreateNetworkInterface` action is required to allow Amazon MQ to create an elastic network interface \(ENI\) in your account on your behalf\.
The `ec2:CreateNetworkInterfacePermission` action authorizes Amazon MQ to attach the ENI to an ActiveMQ broker\.
The `ec2:AuthorizedService` condition key ensures that ENI permissions can be granted only to Amazon MQ service accounts\.

```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Action": [
            "mq:*",
            "[ec2:CreateNetworkInterface](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateNetworkInterface.html)",
            "[ec2:DeleteNetworkInterface](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteNetworkInterface.html)",
            "[ec2:DetachNetworkInterface](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DetachNetworkInterface.html)",
            "[ec2:DescribeInternetGateways](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeInternetGateways.html)",
            "[ec2:DescribeNetworkInterfaces](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeNetworkInterfaces.html)",
            "[ec2:DescribeRouteTables](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeRouteTables.html)",
            "[ec2:DescribeSecurityGroups](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeSecurityGroups.html)",
            "[ec2:DescribeSubnets](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeSubnets.html)",
            "[ec2:DescribeVpcs](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeVpcs.html)"
        ],
        "Effect": "Allow",
        "Resource": "*"
    },{
        "Action": [
            "[ec2:CreateNetworkInterfacePermission](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateNetworkInterfacePermission.html)",
            "[ec2:DeleteNetworkInterfacePermission](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteNetworkInterfacePermission.html)",
            "[ec2:DescribeNetworkInterfacePermission](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeNetworkInterfacePermissions.html)"
        ],
        "Effect": "Allow",
        "Resource": "*",
        "Condition": {
            "StringEquals": {
                "[ec2:AuthorizedService](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonec2.html#amazonec2-ec2_AuthorizedService)": "mq.amazonaws.com"
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
| [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-brokers.html#rest-api-brokers-methods-post](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-brokers.html#rest-api-brokers-methods-post) | mq:CreateBroker | 
| [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configurations.html#rest-api-configurations-methods-post](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configurations.html#rest-api-configurations-methods-post) | mq:CreateConfiguration | 
| [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-user.html#rest-api-user-methods-post](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-user.html#rest-api-user-methods-post) | mq:CreateUser | 
| [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker.html#rest-api-broker-methods-delete](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker.html#rest-api-broker-methods-delete) | mq:DeleteBroker | 
| [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-user.html#rest-api-user-methods-delete](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-user.html#rest-api-user-methods-delete) | mq:DeleteUser | 
| [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker.html#rest-api-broker-methods-get](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker.html#rest-api-broker-methods-get) | mq:DescribeBroker | 
| [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration.html#rest-api-configuration-methods-get](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration.html#rest-api-configuration-methods-get) | mq:DescribeConfiguration | 
| [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration-revision.html#rest-api-configuration-revision-methods-get](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration-revision.html#rest-api-configuration-revision-methods-get) | mq:DescribeConfigurationRevision | 
| [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-user.html#rest-api-user-methods-get](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-user.html#rest-api-user-methods-get) | mq:DescribeUser | 
| [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-brokers.html#rest-api-brokers-methods-get](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-brokers.html#rest-api-brokers-methods-get) | mq:ListBrokers | 
| [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration-revisions.html#rest-api-configuration-revisions-methods-get](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration-revisions.html#rest-api-configuration-revisions-methods-get) | mq:ListConfigurationRevisions | 
| [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configurations.html#rest-api-configurations-methods-get](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configurations.html#rest-api-configurations-methods-get) | mq:ListConfigurations | 
| [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-users.html#rest-api-users-methods-get](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-users.html#rest-api-users-methods-get) | mq:ListUsers | 
| [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker-reboot.html#rest-api-broker-reboot-methods-post](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker-reboot.html#rest-api-broker-reboot-methods-post) | mq:RebootBroker  | 
| [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker.html#rest-api-broker-methods-put](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-broker.html#rest-api-broker-methods-put) | mq:UpdateBroker | 
| [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration.html#rest-api-configuration-methods-put](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-configuration.html#rest-api-configuration-methods-put) | mq:UpdateConfiguration | 
| [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-user.html#rest-api-user-methods-put](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/rest-api-user.html#rest-api-user-methods-put) | mq:UpdateUser | 