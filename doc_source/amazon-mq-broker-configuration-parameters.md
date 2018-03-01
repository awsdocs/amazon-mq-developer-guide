# Amazon MQ Broker Configuration Parameters<a name="amazon-mq-broker-configuration-parameters"></a>

A *configuration* contains all of the settings for your ActiveMQ broker, in XML format \(similar to ActiveMQ's `activemq.xml` file\)\. You can create a configuration before creating any brokers\. You can then apply the configuration to one or more brokers\. For more information, see the following:

+ [Configuration](amazon-mq-basic-elements.md#configuration)

+ [Tutorial: Creating and Applying Amazon MQ Broker Configurations](amazon-mq-creating-applying-configurations.md)

+ [Tutorial: Editing Amazon MQ Broker Configurations and Managing Configuration Revisions](amazon-mq-editing-managing-configurations.md)

+ [Configurations](amazon-mq-limits.md#configuration-limits)

## Working with Spring XML Configuration Files<a name="working-with-spring-xml-configuration-files"></a>

ActiveMQ brokers are configured using [Spring XML](https://docs.spring.io/spring/docs/current/spring-framework-reference/) files\. You can configure many aspects of your ActiveMQ broker, such as predefined destinations, destination policies, authorization policies, and plugins\. Amazon MQ controls some of these configuration elements, such as network transports and storage\. Other configuration options, such as creating networks of brokers, aren't currently supported\.

The full set of supported configuration options is specified in the [Amazon MQ XML schema](https://s3-us-west-2.amazonaws.com/amazon-mq-docs/XML/amazon-mq-active-mq-5.15.0.xsd)\. You can use this schema to validate and sanitize your configuration files\. Amazon MQ also lets you provide configurations by uploading XML files\. When you upload an XML file, Amazon MQ automatically sanitizes and removes invalid and prohibited configuration parameters according to the schema\.

**Note**  
You can use only static values for attributes\. Amazon MQ sanitizes elements and attributes that contain Spring expressions, variables, and element references from your configuration\.


+ [Working with Spring XML Configuration Files](#working-with-spring-xml-configuration-files)
+ [Permitted Elements](permitted-elements.md)
+ [Permitted Attributes](permitted-attributes.md)
+ [Permitted Collections](permitted-collections.md)