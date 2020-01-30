---
layout: default
title: "Installing on-premises Pipelines with hybrid AWS"
---

Pipelines AWS Hybrid installation is a secenario where Pipelines is installed on a server that is not in the AWS cloud, but still relies on AWS resources to operate. These AWS services include:
<ul>
<li>S3 Bucket</li>
<li>Dynamo DB</li>
</ul>

<h3>Create an S3 Bucket</h3>

Before you can start, you will need to setup an S3 bucket in AWS. As an option, you can run the [CloudFormation S3 Bucket Creation Script](./onpremises-aws.html#s3-bucket-creation).

If creating the bucket manually, ensure you know the region you are creating it in. This is important.


<h3>Create an IAM User</h3>

For the server to have access to AWS resources, including S3 and DynamoDB, it will need IAM security credentials.

<h4>IAM User S3 Policy</h4>

The IAM user must have access to the S3 bucket in its region. This <b>Allow</b> access must include:
<table>
<tr><th>Action</th><th>Resource</th></tr>
<tr><td>s3:ListBucket</td><td>arn:aws:s3:::BUCKET</td></tr>
<tr><td>s3:GetBucketLocation</td><td>arn:aws:s3:::BUCKET</td></tr>
<tr><td>s3:GetObject</td><td>arn:aws:s3:::BUCKET/&#42;</td></tr>
<tr><td>s3:PutObject</td><td>arn:aws:s3:::BUCKET/&#42;</td></tr>
<tr><td>s3:DeleteObject</td><td>arn:aws:s3:::BUCKET/&#42;</td></tr>
</table>

Here is an example IAM Policy, in this example BUCKET is named <b>distelli-onprem</b>:

~~~
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": "arn:aws:s3:::distelli-onprem"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject",
                "s3:DeleteObject"
            ],
            "Resource": "arn:aws:s3:::distelli-onprem/*"
        }
    ]
}
~~~

<h4>IAM User DynamoDB Policy</h4>

When running the Pipelines install script, you can specify a prefix for you DynamoDB tables. This can provide you a mechanism to lock down Pipelines's access to your DynamoDB to only tables with this prefix. Here is an example policy, note the <b>PREFIX</b> followed by an asterisk.

~~~
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1465427217000",
            "Effect": "Allow",
            "Action": [
                "dynamodb:BatchGetItem",
                "dynamodb:BatchWriteItem",
                "dynamodb:CreateTable",
                "dynamodb:DeleteItem",
                "dynamodb:DeleteTable",
                "dynamodb:DescribeReservedCapacity",
                "dynamodb:DescribeReservedCapacityOfferings",
                "dynamodb:DescribeStream",
                "dynamodb:DescribeTable",
                "dynamodb:GetItem",
                "dynamodb:GetRecords",
                "dynamodb:GetShardIterator",
                "dynamodb:ListStreams",
                "dynamodb:ListTables",
                "dynamodb:PurchaseReservedCapacityOfferings",
                "dynamodb:PutItem",
                "dynamodb:Query",
                "dynamodb:Scan",
                "dynamodb:UpdateItem",
                "dynamodb:UpdateTable"
            ],
            "Resource": [
                "arn:aws:dynamodb:eu-west-1:708141427824:table/<b>PREFIX</b>*"
            ]
        }
    ]
}
~~~

<h3>Obtain the Pipelines on premises Install Script</h3>

Please contact our sales team to obtain the Pipelines on premises install script.
