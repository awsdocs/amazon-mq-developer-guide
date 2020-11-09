# Recovering from network failures<a name="best-practices-rabbitmq-connection-recovery"></a>

We recommend always enabling automatic network recovery to prevent significant downtime in cases where client connections to RabbitMQ nodes fail\. The RabbitMQ Java client library supports automatic network recovery by default, beginning with version `4.0.0`\.

Automatic connection recovery is triggered if an unhandled exception is thrown in the connection's I/O loop, if a socket read operation timeout is detected, or if the server misses a [heartbeat](https://www.rabbitmq.com/heartbeats.html)\.

In cases where the initial connection between a client and a RabbitMQ node fails, automatic recovery will not be triggered\. We recommend writing your application code to account for initial connection failures by retrying the connection\. The following example demonstrates retrying initial network failures using the RabbitMQ Java client library\.

```
ConnectionFactory factory = new ConnectionFactory();
// enable automatic recovery if using RabbitMQ Java client library prior to version 4.0.0.
factory.setAutomaticRecoveryEnabled(true);
// configure various connection settings

try {
  Connection conn = factory.newConnection();
} catch (java.net.ConnectException e) {
  Thread.sleep(5000);
  // apply retry logic
}
```

**Note**  
If an application closes a connection by using the `Connection.Close` method, automatic network recovery will not be enabled or triggered\.