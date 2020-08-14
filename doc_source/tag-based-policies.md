# Tag\-based Policies<a name="tag-based-policies"></a>

Amazon MQ supports policies based on tags\. For instance, you could deny access to Amazon MQ resources that include a tag with the key `environment` and the value `production`:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "Action": [
                "mq:DeleteBroker",
                "mq:RebootBroker",
                "mq:DeleteTag"
            ],
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "aws:ResourceTag/environment": "production"
                }
            }
        }
    ]
}
```

This policy will `Deny` the ability to delete or reboot an Amazon MQ broker that includes the tag `environment/production`\.

For more information on tagging, see:
+ [Tagging resources](amazon-mq-tagging.md)
+ [Controlling Access Using IAM Tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_iam-tags.html)