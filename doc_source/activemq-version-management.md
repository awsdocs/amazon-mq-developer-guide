# Managing Amazon MQ for ActiveMQ engine versions<a name="activemq-version-management"></a>

 Amazon MQ for ActiveMQ supports creating brokers using the following major versions of Apache ActiveMQ\. 
+ 5\.16
+ 5\.15

**Topics**
+ [Major and minor version upgrades](#w77aac19c11c13c17)
+ [Listing supported engine versions](#w77aac19c11c13c19)

 Apache ActiveMQ organizes version numbers according to semantic versioning specification as `X.Y.Z`\. In Amazon MQ for ActiveMQ implementations, `X.Y` denotes the major version, and `Z` represents the minor version number\. Amazon MQ considers a version change to be major if the major version numbers change\. For example, upgrading from version 5\.**15** to 5\.**16** is considered a *major version upgrade*\. A version change is considered minor if only the minor version number changes\. For example, upgrading from version 5\.15\.**14** to 5\.15\.**15** is considered a *minor version upgrade*\. 

 Amazon MQ for ActiveMQ currently supports the following engine versions of Apache ActiveMQ\. 


| Major versions | Minor versions | 
| --- | --- | 
| ActiveMQ 5\.16 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/activemq-version-management.html)  | 
| ActiveMQ 5\.15 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/amazon-mq/latest/developer-guide/activemq-version-management.html)  | 

 When you create a new Amazon MQ for ActiveMQ broker, you can specify any supported ActiveMQ engine version\. If you use the AWS Management Console to create a broker, Amazon MQ automatically defaults to the latest engine version number\. If you use the AWS CLI or the Amazon MQ API to create a broker, the engine version number is required\. If you don't provide a version number, the operation will result in an exception\. To learn more, see [https://docs.aws.amazon.com/cli/latest/reference/mq/create-broker](https://docs.aws.amazon.com/cli/latest/reference/mq/create-broker) in the *AWS CLI Command Reference* and [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers.html#CreateBroker](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers.html#CreateBroker) in the *Amazon MQ REST API Reference*\. 

## Major and minor version upgrades<a name="w77aac19c11c13c17"></a>

 With Amazon MQ, you control when to upgrade your brokers to new versions\. When [ automatic minor version upgrade](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id.html#brokers-broker-id-prop-updatebrokerinput-autominorversionupgrade) is activated, Amazon MQ will automatically upgrade your broker engine to new ActiveMQ minor versions as they are released and supported by Amazon MQ\. You can [modify broker preferences](https://docs.aws.amazon.com/amazon-mq/latest/developer-guide/amazon-mq-editing-broker-preferences.html) to activate or deactivate automatic minor version upgrades\. To perform a major version upgrade, for example, from ActiveMQ 5\.15 to 5\.16, you must manually upgrade your broker's engine version number\. Minor and major version upgrades occure at the same time as other broker patching operations, during your scheduled maintenance window\. 

 If you opt out of automatic minor version upgrades, you can manually upgrade your broker to a supported minor version by following the same procedure as a major version upgrade\. To learn more about munual upgrades, see [Upgrading an Amazon MQ broker engine version](upgrading-brokers.md)\. 

## Listing supported engine versions<a name="w77aac19c11c13c19"></a>

 You can list all supported minor and major engine versions by using the [https://docs.aws.amazon.com/cli/latest/reference/mq/describe-broker-instance-options.html](https://docs.aws.amazon.com/cli/latest/reference/mq/describe-broker-instance-options.html) AWS CLI command\. 

```
aws mq describe-broker-instance-options
```

To filter the results by engine and instance type use the `--engine-type` and `--host-instance-type` options as shown in the following\.

```
aws mq describe-broker-instance-options --engine-type engine-type --host-instance-type instance-type
```

For example, to filter the results for ActiveMQ, and `mq.m5.large` instance type, replace *engine\-type* with `ACTIVEMQ` and *instance\-type* with `mq.m5.large`\.