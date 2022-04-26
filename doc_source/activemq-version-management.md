# Managing Amazon MQ for ActiveMQ engine versions<a name="activemq-version-management"></a>

 Apache ActiveMQ organizes version numbers according to semantic versioning specification as `X.Y.Z`\. In Amazon MQ for ActiveMQ implementations, `X.Y` denotes the major version, and `Z` represents the minor version number\. Amazon MQ considers a version change to be major if the major version numbers change\. For example, upgrading from version 5\.**15** to 5\.**16** is considered a *major version upgrade*\. A version change is considered minor if only the minor version number changes\. For example, upgrading from version 5\.15\.**14** to 5\.15\.**15** is considered a *minor version upgrade*\. 

 Amazon MQ for ActiveMQ currently supports the following engine versions of Apache ActiveMQ\. 


| Major versions | Minor versions | 
| --- | --- | 
| ActiveMQ 5\.16 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/activemq-version-management.html)  | 
| ActiveMQ 5\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/activemq-version-management.html)  | 

**Note**  
 ActiveMQ 5\.15\.15 is the last minor version planned for the 5\.15\.x release\. We recommend upgrading your brokers to the latest supported ActiveMQ major engine version, 5\.16\.x\. 

 When you create a new Amazon MQ for ActiveMQ broker, you can specify any supported ActiveMQ engine version\. If you use the AWS Management Console to create a broker, Amazon MQ automatically defaults to the latest engine version number\. If you use the AWS CLI or the Amazon MQ API to create a broker, the engine version number is required\. If you don't provide a version number, the operation will result in an exception\. To learn more, see [https://docs.aws.amazon.com/cli/latest/reference/mq/create-broker](https://docs.aws.amazon.com/cli/latest/reference/mq/create-broker) in the *AWS CLI Command Reference* and [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers.html#CreateBroker](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers.html#CreateBroker) in the *Amazon MQ REST API Reference*\. 

**Topics**
+ [Major and minor version upgrades](#activemq-version-management-upgrading)
+ [Listing supported engine versions](#activemq-version-management-listing-versions)

## Major and minor version upgrades<a name="activemq-version-management-upgrading"></a>

 With Amazon MQ, you control when to upgrade your brokers to new versions\. When [ automatic minor version upgrade](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id.html#brokers-broker-id-prop-updatebrokerinput-autominorversionupgrade) is activated, Amazon MQ will automatically upgrade your broker engine to new minor versions as they are released and supported by Amazon MQ\. 

 To perform a major version upgrade, you must manually upgrade your broker's engine version number\. Minor and major version upgrades occure at the same time as other broker patching operations, during your scheduled [maintenance window](maintaining-brokers.md)\. If you opt out of automatic minor version upgrades, you can manually upgrade your broker to a new supported minor version by following the same procedure as a major upgrade\. 

 For more information about updating your broker preferences to activate or deactivate minor version upgrades, and manually upgrading your broker, see [Upgrading an Amazon MQ broker engine version](upgrading-brokers.md)\. 

## Listing supported engine versions<a name="activemq-version-management-listing-versions"></a>

 You can list all supported minor and major engine versions by using the [https://docs.aws.amazon.com/cli/latest/reference/mq/describe-broker-instance-options.html](https://docs.aws.amazon.com/cli/latest/reference/mq/describe-broker-instance-options.html) AWS CLI command\. 

```
aws mq describe-broker-instance-options
```

To filter the results by engine and instance type use the `--engine-type` and `--host-instance-type` options as shown in the following\.

```
aws mq describe-broker-instance-options --engine-type engine-type --host-instance-type instance-type
```

For example, to filter the results for ActiveMQ, and `mq.m5.large` instance type, replace *engine\-type* with `ACTIVEMQ` and *instance\-type* with `mq.m5.large`\.