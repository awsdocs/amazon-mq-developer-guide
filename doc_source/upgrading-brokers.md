# Upgrading an Amazon MQ broker engine version<a name="upgrading-brokers"></a>

 Amazon MQ provides new broker engine versions for all supported broker engine type\. New engine versions might include security patches, bug fixes and other broker engine improvements\. When Amazon MQ supports a new engine version, you can control how and when to upgrade your broker\. 

 Broker engine versions are organized as `X.Y.Z`\. In the Amazon MQ implementation of each engine type, `X.Y` is considered a major version and `Z` is considered a minor version\. There are two types of upgrades: 
+ **Major version upgrade** – Occures when the major engine version numbers change\. For example, upgrading from version 1\.**0** to version 1\.**1** is considered a major version upgrade\. 
+ **Minor version upgrade** – Occures when only the minor engine version number changes\. For example, upgrading from version 1\.1\.**0** to version 1\.1\.**1** is considered a minor version upgrade\. 

 For more information about major and minor version management for each specific broker engine type, see the following topics\. 
+ [Managing Amazon MQ for ActiveMQ engine versions](activemq-version-management.md)
+ [Managing Amazon MQ for RabbitMQ engine versions](rabbitmq-version-management.md)

When you activate the[ automatic minor version upgrade](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id.html#brokers-broker-id-prop-updatebrokerinput-autominorversionupgrade) option, Amazon MQ upgrades your broker to new minor versions as they become available\. Automatic minor version upgrades occur only if the broker is running a minor engine version that is lower than the new recommended minor version\. For major upgrades, you must manually upgrade the engine version\. 

 Both manual and automatic version upgrades occur during the scheduled maintenance window or after you [reboot your broker](amazon-mq-rebooting-broker.md)\.

The following topics describe how you can manually upgrade the broker engine version, and activate automatic minor version upgrades\.

**Topics**
+ [Manually upgrading the engine version](#upgrading-brokers-manual-upgrades)
+ [Automatically upgrading the minor engine version](#upgrading-brokers-automatic-upgrades)

## Manually upgrading the engine version<a name="upgrading-brokers-manual-upgrades"></a>

 To manually upgrade the engine version of a broker to a new major or minor version, you can use the AWS Management Console, the AWS CLI, or the Amazon MQ API\. 

### AWS Management Console<a name="upgrading-brokers-manual-upgrades-console"></a>

**To upgrade the engine version of a broker by using the AWS Management Console**

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. In the left navigation pane, choose **Brokers**, and then choose the broker that you want to upgrade from the list\.

1.  On the broker details page, choose **Edit**\. 

1.  Under **Specifications**, for **Broker engine version** choose the new version number from the dropdown list\. 

1. Scroll to the bottom of the page, and choose **Schedule modifications**\.

1.  On the **Schedule broker modifications** page, for **When to apply modifications**, choose one of the following\. 
   +  Choose **After the next reboot**, if you want Amazon MQ to complete the version upgrade during the next scheduled maintenance window\. 
   +  Choose **Immediately**, if you want to reboot the broker and upgrade the engine version immediately\. 
**Important**  
Your broker will be offline while it is being rebooted\.

1.  Choose **Apply** to finish applying the changes\. 

### AWS CLI<a name="upgrading-brokers-manual-upgrades-cli"></a>

**To upgrade the engine version of a broker by using the AWS CLI**

1.  Use the [update\-broker](https://docs.aws.amazon.com/cli/latest/reference/mq/update-broker.html) CLI command and specify the following parameters, as shown in the example\. 
   +  `--broker-id` – The unique ID that Amazon MQ generates for the broker\. You can parse the ID from your broker ARN\. For example, given the following ARN, `arn:aws:mq:us-east-2:123456789012:broker:MyBroker:b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`, the broker ID would be `b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`\. 
   +  `--engine-version` – The engine version number for the broker engine to upgrade to\. 

   ```
   aws mq update-broker --broker-id broker-id --engine-version version-number
   ```

1.  \(Optional\) Use the [reboot\-broker](https://docs.aws.amazon.com/cli/latest/reference/mq/reboot-broker.html) CLI command to reboot your broker if, you want to upgrade the engine version immediately\. 

   ```
   aws mq reboot-broker --broker-id broker-id
   ```

   If you do not want to reboot your broker and apply the changes immediately, Amazon MQ will upgrade the broker during the next scheduled maintenance window\.
**Important**  
Your broker will be offline while it is being rebooted\.

### Amazon MQ API<a name="upgrading-brokers-manual-upgrades-api"></a>

**To upgrade the engine version of a broker by using the Amazon MQ API**

1.  Use the [UpdateBroker](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id.html#UpdateBroker) API operation\. Specify `broker-id` as a path parameter\. The following examples assumes a broker in the `us-west-2` region\. For more information about available Amazon MQ endpoints, see [Amazon MQ endpoints and quotas\.](https://docs.aws.amazon.com/general/latest/gr/amazon-mq.html#amazon-mq_region) in the *AWS General Reference* 

   ```
   PUT /v1/brokers/broker-id HTTP/1.1
   Host: mq.us-west-2.amazonaws.com
   Date: Mon, 7 June 2021 12:00:00 GMT
   x-amz-date: Mon, 7 June 2021 12:00:00 GMT
   Authorization: authorization-string
   ```

   Use `engineVersion` in the request payload to specify the version number for the broker to upgrade to\.

   ```
   {
       "engineVersion": "engine-version-number"
   }
   ```

1.  \(Optional\) Use the [RebootBroker](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id-reboot.html#RebootBroker) API operation to reboot your broker, if you want to upgrade the engine version immediately\. `broker-id` is specified as a path parameter\. 

   ```
   POST /v1/brokers/broker-id/reboot-broker HTTP/1.1
   Host: mq.us-west-2.amazonaws.com
   Date: Mon, 7 June 2021 12:00:00 GMT
   x-amz-date: Mon, 7 June 2021 12:00:00 GMT
   Authorization: authorization-string
   ```

   If you do not want to reboot your broker and apply the changes immediately, Amazon MQ will upgrade the broker during the next scheduled maintenance window\.
**Important**  
Your broker will be offline while it is being rebooted\.

## Automatically upgrading the minor engine version<a name="upgrading-brokers-automatic-upgrades"></a>

 You can control whether automatic minor version upgrade is activated for a broker when you first create the broker, or by modifying broker preferences\. To activate auto minor version upgrades for an existing broker, you can use the AWS Management Console, the AWS CLI, or the Amazon MQ API\. 

### AWS Management Console<a name="upgrading-brokers-automatic-upgrades-console"></a>

**To activate automatic minor version upgrades by using the AWS Management Console**

1. Sign in to the [Amazon MQ console](https://console.aws.amazon.com/amazon-mq/)\.

1. In the left navigation pane, choose **Brokers**, and then choose the broker that you want to upgrade from the list\.

1.  On the broker details page, choose **Edit**\. 

1.  Under **Maintenance**, choose **Enable automatic minor version upgrades**\. 
**Note**  
 If the option is already selected, you do not need to make any changes\. 

1. Choose **Save** at the bottom of the page\.

### AWS CLI<a name="upgrading-brokers-automatic-upgrades-cli"></a>

 To activate automatic minor version upgrades via the AWS CLI, use the [update\-broker](https://docs.aws.amazon.com/cli/latest/reference/mq/update-broker.html) CLI command and specify the following parameters\. 
+  `--broker-id` – The unique ID that Amazon MQ generates for the broker\. You can parse the ID from your broker ARN\. For example, given the following ARN, `arn:aws:mq:us-east-2:123456789012:broker:MyBroker:b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`, the broker ID would be `b-1234a5b6-78cd-901e-2fgh-3i45j6k178l9`\. 
+  `--auto-minor-version-upgrade` – Activates the auto minor version upgrade option\. 

```
aws mq update-broker --broker-id broker-id --auto-minor-version-upgrade
```

If you want to deactivate auto minor version upgrades for your broker, use the `--no-auto-minor-version-upgrade` parameter\.

### Amazon MQ API<a name="upgrading-brokers-automatic-upgrades-api"></a>

 To activate automatic minor version upgrades via the Amazon MQ API, use the [UpdateBroker](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id.html#UpdateBroker) API operation\. Specify `broker-id` as a path parameter\. The following example assumes a broker in the `us-west-2` region\. For more information about available Amazon MQ endpoints, see [Amazon MQ endpoints and quotas\.](https://docs.aws.amazon.com/general/latest/gr/amazon-mq.html#amazon-mq_region) in the *AWS General Reference* 

```
PUT /v1/brokers/broker-id HTTP/1.1
Host: mq.us-west-2.amazonaws.com
Date: Mon, 7 June 2021 12:00:00 GMT
x-amz-date: Mon, 7 June 2021 12:00:00 GMT
Authorization: authorization-string
```

Use the `autoMinorVersionUpgrade` property in the request payload to activate auto minor version upgrade\.

```
{
    "autoMinorVersionUpgrade": "true"
}
```

If you want to deactivate auto minor version upgrades for your broker, set `"autoMinorVersionUpgrade": "false"` in the request payload\.