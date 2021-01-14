# Using service\-linked roles for Amazon MQ<a name="using-service-linked-roles"></a>

Amazon MQ uses AWS Identity and Access Management \(IAM\)[ service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role)\. A service\-linked role is a unique type of IAM role that is linked directly to Amazon MQ\. Service\-linked roles are predefined by Amazon MQ and include all the permissions that the service requires to call other AWS services on your behalf\. 

A service\-linked role makes setting up Amazon MQ easier because you don’t have to manually add the necessary permissions\. Amazon MQ defines the permissions of its service\-linked roles, and unless defined otherwise, only Amazon MQ can assume its roles\. The defined permissions include the trust policy and the permissions policy, and that permissions policy cannot be attached to any other IAM entity\.

You can delete a service\-linked role only after first deleting their related resources\. This protects your Amazon MQ resources because you can't inadvertently remove permission to access the resources\.

For information about other services that support service\-linked roles, see [AWS Services That Work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html) and look for the services that have **Yes **in the **Service\-Linked Role** column\. Choose a **Yes** with a link to view the service\-linked role documentation for that service\.

## Service\-linked role permissions for Amazon MQ<a name="slr-permissions"></a>

Amazon MQ uses the service\-linked role named **AWSServiceRoleForAmazonMQ** – Amazon MQ uses this service\-linked role to call AWS services on your behalf\.

The AWSServiceRoleForAmazonMQ service\-linked role trusts the following services to assume the role:
+ `mq.amazonaws.com`

The role permissions policy allows Amazon MQ to complete the following actions on the specified resources:
+ Action: `ec2:CreateVpcEndpoint` on the `vpc` resource\.

  
+ Action: `ec2:CreateVpcEndpoint` on the `subnet` resource\.

  
+ Action: `ec2:CreateVpcEndpoint` on the `security-group` resource\.

  
+ Action: `ec2:CreateVpcEndpoint` on the `vpc-endpoint` resource\.

  
+ Action: `ec2:DescribeVpcEndpoints` on the `vpc` resource\.

  
+ Action: `ec2:DescribeVpcEndpoints` on the `subnet` resource\.

  
+ Action: `ec2:CreateTags` on the `vpc-endpoint` resource\.

  
+ Action: `logs:PutLogEvents` on the `log-group` resource\.

  
+ Action: `logs:DescribeLogStreams` on the `log-group` resource\.

  
+ Action: `logs:DescribeLogGroups` on the `log-group` resource\.

  
+ Action: `CreateLogStream` on the `log-group` resource\.

  
+ Action: `CreateLogGroup` on the `log-group` resource\.

  

You must configure permissions to allow an IAM entity \(such as a user, group, or role\) to create, edit, or delete a service\-linked role\. For more information, see [Service\-Linked Role Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#service-linked-role-permissions) in the *IAM User Guide*\.

## Creating a service\-linked role for Amazon MQ<a name="create-slr"></a>

You don't need to manually create a service\-linked role\. When you first create a broker, Amazon MQ creates a service\-linked role to call AWS services on your behalf\. All subsequent brokers that you create will use the same role and no new role is created\.

**Important**  
This service\-linked role can appear in your account if you completed an action in another service that uses the features supported by this role\. To learn more, see [A New Role Appeared in My IAM Account](https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_roles.html#troubleshoot_roles_new-role-appeared)\.

If you delete this service\-linked role, and then need to create it again, you can use the same process to recreate the role in your account\.

You can also use the IAM console to create a service\-linked role with the **Amazon MQ** use case\. In the AWS CLI or the AWS API, create a service\-linked role with the `mq.amazonaws.com` service name\. For more information, see [Creating a service\-linked role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#create-service-linked-role) in the *IAM User Guide*\. If you delete this service\-linked role, you can use this same process to create the role again\.

## Editing a service\-linked role for Amazon MQ<a name="edit-slr"></a>

Amazon MQ does not allow you to edit the AWSServiceRoleForAmazonMQ service\-linked role\. However, you can edit the description of the role using IAM\. For more information, see [Editing a service\-linked role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#edit-service-linked-role) in the *IAM User Guide*\.

## Deleting a service\-linked role for Amazon MQ<a name="delete-slr"></a>

If you no longer need to use a feature or service that requires a service\-linked role, we recommend that you delete that role\. That way you don’t have an unused entity that is not actively monitored or maintained\. However, you must clean up the resources for your service\-linked role before you can manually delete it\.

**Note**  
If the Amazon MQ service is using the role when you try to delete the resources, then the deletion might fail\. If that happens, wait for a few minutes and try the operation again\.

**To delete Amazon MQ resources used by the AWSServiceRoleForAmazonMQ**
+ Delete your Amazon MQ brokers using the AWS Management Console, Amazon MQ CLI, or Amazon MQ API\. For more information about deleting brokers, see [Deleting a broker](amazon-mq-deleting-broker.md)\.

**To manually delete the service\-linked role using IAM**

Use the IAM console, the AWS CLI, or the AWS API to delete the AWSServiceRoleForAmazonMQ service\-linked role\. For more information, see [Deleting a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#delete-service-linked-role) in the *IAM User Guide*\.

## Supported regions for Amazon MQ service\-linked roles<a name="slr-regions"></a>

Amazon MQ supports using service\-linked roles in all of the regions where the service is available\. For more information, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)\.


****  

| Region name | Region identity | Support in Amazon MQ | 
| --- | --- | --- | 
| US East \(N\. Virginia\) | us\-east\-1 | Yes | 
| US East \(Ohio\) | us\-east\-2 | Yes | 
| US West \(N\. California\) | us\-west\-1 | Yes | 
| US West \(Oregon\) | us\-west\-2 | Yes | 
| Asia Pacific \(Mumbai\) | ap\-south\-1 | Yes | 
| Asia Pacific \(Osaka\-Local\) | ap\-northeast\-3 | Yes | 
| Asia Pacific \(Seoul\) | ap\-northeast\-2 | Yes | 
| Asia Pacific \(Singapore\) | ap\-southeast\-1 | Yes | 
| Asia Pacific \(Sydney\) | ap\-southeast\-2 | Yes | 
| Asia Pacific \(Tokyo\) | ap\-northeast\-1 | Yes | 
| Canada \(Central\) | ca\-central\-1 | Yes | 
| Europe \(Frankfurt\) | eu\-central\-1 | Yes | 
| Europe \(Ireland\) | eu\-west\-1 | Yes | 
| Europe \(London\) | eu\-west\-2 | Yes | 
| Europe \(Paris\) | eu\-west\-3 | Yes | 
| South America \(São Paulo\) | sa\-east\-1 | Yes | 
| AWS GovCloud \(US\) | us\-gov\-west\-1 | No | 