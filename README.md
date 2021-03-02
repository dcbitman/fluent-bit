### https://docs.amazonaws.cn/en_us/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-logs-FluentBit.html

```
ClusterName=cluster-name
RegionName=us-gov-west-1
FluentBitHttpPort='2020'
FluentBitReadFromHead='Off'
[[ ${FluentBitReadFromHead} = 'On' ]] && FluentBitReadFromTail='Off'|| FluentBitReadFromTail='On'
[[ -z ${FluentBitHttpPort} ]] && FluentBitHttpServer='Off' || FluentBitHttpServer='On'
kubectl create configmap fluent-bit-cluster-info \
--from-literal=cluster.name=${ClusterName} \
--from-literal=http.server=${FluentBitHttpServer} \
--from-literal=http.port=${FluentBitHttpPort} \
--from-literal=read.head=${FluentBitReadFromHead} \
--from-literal=read.tail=${FluentBitReadFromTail} \
--from-literal=logs.region=${RegionName} -n logging
```


Apply config
```
kubectl apply -f fluent-bits.yaml
```

**IAM Policies**

To attach the necessary policy to the IAM role for your worker nodes

Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/.
Select one of the worker node instances and choose the IAM role in the description.

On the IAM role page, choose Attach policies.

In the list of policies, select the check box next to CloudWatchAgentServerPolicy. If necessary, use the search box to find this policy.

Choose Attach policies.

- CloudWatchAgentServerPolicy
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "cloudwatch:PutMetricData",
                "ec2:DescribeVolumes",
                "ec2:DescribeTags",
                "logs:PutLogEvents",
                "logs:DescribeLogStreams",
                "logs:DescribeLogGroups",
                "logs:CreateLogStream",
                "logs:CreateLogGroup"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ssm:GetParameter"
            ],
            "Resource": "arn:aws-us-gov:ssm:*:*:parameter/AmazonCloudWatch-*"
        }
    ]
}
```

- AmazonS3LogAccess
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": "arn:aws-us-gov:s3:::stitches-logs"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObject"
            ],
            "Resource": "arn:aws-us-gov:s3:::stitches-logs/*"
        }
    ]
}
```

Dot mil emplimentaiton will require
https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/cloudwatch-logs-and-interface-VPC.html
https://docs.aws.amazon.com/AmazonS3/latest/userguide/privatelink-interface-endpoints.html#privatelink-limitations
# fluent-bit
