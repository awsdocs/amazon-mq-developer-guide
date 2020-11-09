# User<a name="rabbitmq-basic-elements-user"></a>

 Every AMQP 0\-9\-1 client connection has an associated user which must be authenticated\. Each client connection also targets a virtual host \(vhost\) for which the user must have a certain set of permissions\. User credentials, and the target vhost are specified at the time the connection is established\. 

 When you first create a RabbitMQ broker, Amazon MQ uses the username and password you provide to create a RabbitMQ user with the `administrator` tag\. You can then add and manage users via the RabbitMQ [management API](https://pulse.mozilla.org/api/) or the RabbitMQ web console\. 

 For example, you can create a new user by creating a `POST` request to `/api/users/user` with the following request body\. 

```
{"password":"secret","tags":"administrator"}
```

**Note**  
The `tags` key is mandatory, and is a comma\-separated list of tags for the user\. Currently, RabbitMQ supports `administrator`, `management`, and `monitoring` tage\.

To add a permission set for a virtual host to an individual user, you can create a `POST` request to `/api/users/vhost/user` with the following request body\.

```
{"configure":".*","write":".*","read":".*"}
```

**Note**  
The `configure`, `read`, and `write` keys are all mandatory\.

This operation will grant read, write, and configure permissions for the specified virtual host to the user\. For more information about managing users via the RabbitMQ management API, see [RabbitMQ Management HTTP API](https://pulse.mozilla.org/api/)\.

**Note**  
RabbitMQ users will not be stored or displayed via the Amazon MQ `users` API\.