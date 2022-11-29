# Setting up Amazon MQ<a name="amazon-mq-setting-up"></a>

Before you can use Amazon MQ, you must complete the following steps\.

**Topics**
+ [Step 1: create an AWS account and an IAM administrator user](#create-aws-account)
+ [Step 2: create an IAM user and get your AWS credentials](#create-iam-user)
+ [Get Ready to Use the Example Code](#get-ready-to-use-example-code)
+ [Next steps](#next-steps-setting-up)

## Step 1: create an AWS account and an IAM administrator user<a name="create-aws-account"></a>

To access any AWS service, you must first create an [Amazon Web Services account](https://aws.amazon.com/)\. This is an Amazon account that can use AWS products\. You can use your AWS account to view your activity and usage reports and to manage authentication and access\.

1. Navigate to the [AWS home page](https://aws.amazon.com/), and then choose **Create an AWS Account**\.

1. Follow the instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a PIN using the phone keypad\.

1. When you finish creating your AWS account, follow the instructions in the *IAM User Guide* to [create your first IAM administrator user and group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html)\.

## Step 2: create an IAM user and get your AWS credentials<a name="create-iam-user"></a>

To avoid using your IAM administrator user for Amazon MQ operations, it is a best practice to create an IAM user for each person who needs administrative access to Amazon MQ\.

To work with Amazon MQ, you need the `AmazonMQFullAccess` policy and AWS credentials that are associated with your IAM user\. These credentials are comprised of an access key ID and a secret access key\. For more information, see [What is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/IAM_Introduction.html) in the *IAM User Guide* and [AWS Security credentials](https://docs.aws.amazon.com/general/latest/gr/aws-security-credentials.html) in the *AWS General Reference*\.

1. Sign in to the [AWS Identity and Access Management console](https://console.aws.amazon.com/iam/)\.

1. Choose **Users**, **Add user**\.

1. Type a **User name**, such as `AmazonMQAdmin`\.

1. Select **Programmatic access** and **AWS Management Console access**\.

1. Set a **Console password** and then choose **Next: Permissions**\.

1. On the **Set permissions for *AmazonMQAdmin*** page, choose **Attach existing policies directly**\.

1. Type `AmazonMQ` into the filter, choose **AmazonMQFullAccess**, and then choose **Next: Review**\.

1. On the **Review** page, choose **Create user**\.

   The IAM user is created and the **Access key ID** is displayed, for example:

   **AKIAIOSFODNN7EXAMPLE**

1. To display your **Secret access key**, choose **Show**, for example:

   **wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY**
**Important**  
You can view or download your secret access key *only* when you create your credentials \(however, you can create new credentials at any time\)\.

1. To download your credentials, choose **Download \.csv**\. Keep this file in a secure location\.

## Step 3: get ready to use the example codes<a name="get-ready-to-use-example-code"></a>

The following tutorials show how you can work with Amazon MQ brokers using the AWS Management Console as well as how to connect to your Amazon MQ for ActiveMQ and Amazon MQ for RabbitMQ brokers programatically\. To use the ActiveMQ Java example code, you must install the [Java Standard Edition Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) and make some changes to the code\.

You can also create and manage brokers programmatically using Amazon MQ [REST API](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/) and AWS SDKs\.

## Next steps<a name="next-steps-setting-up"></a>

Now that you're prepared to work with Amazon MQ, get started by [creating a broker](amazon-mq-getting-started.md)\. Depending on your broker engine type, you can then [connect a Java application to your Amazon MQ for ActiveMQ broker](getting-started-activemq.md#connect-java-application) or use the RabbitMQ Java client library to [connect a JVM\-based application to your Amazon MQ for RabbitMQ broker](getting-started-rabbitmq.md#rabbitmq-connect-jvm-application)\.
