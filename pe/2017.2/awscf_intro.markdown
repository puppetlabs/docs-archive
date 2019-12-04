---
layout: default
title: "Puppet AWS CloudFormation User Guide"
canonical: "/pe/latest/awscf_intro.html"
description: "Instructions on deploying Puppet Enterprise using Amazon Web Services (AWS) CloudFormation templates."
---

AWS CloudFormation templates allow you to define and deploy all of the resources you need for an application in the Amazon Web Services cloud. Puppet's AWS CloudFormation templates can deploy a Puppet Enterprise master in a CloudFormation stack, construct concise templates with simple Classes and Builders, and version and publish templates.

For more information about CloudFormation templates, see the [AWS documentation](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/GettingStarted.Walkthrough.html).

## Configuring the AWS command-line interface

You must have an AWS account to use AWS CloudFormation. Before you can use Puppet CloudFormation templates, you must configure your AWS credentials and the private AWS key pair in the correct location.

1.  Install the AWS command-line interface (`awscli`) using `pip`.

    ```
    pip install awscli
    ```

2.  Set your AWS access key and default AWS region. If you need your access key ID and secret access key, log into your AWS account and view them from the console.

    ```
    aws configure
    AWS Access Key ID [None]: [Value from https://aws.amazon.com/console/]
    AWS Secret Access Key [None]: [Value from https://aws.amazon.com/console/]
    Default region name [None]: [Selected AWS Region]
    Default output format [None]:
    ```

3.  Verify that the tools work by requesting a list of available EC2 regions.

    ```
    aws ec2 describe-regions
    ```

    If this fails, run `aws configure` again and confirm that all values are correct.

4.  Create an AWS key pair, store the private key locally, and set secure permissions on the private key.

    ```
    aws ec2 create-key-pair --key-name $USER --query KeyMaterial --output text > ~/.aws/${USER}.pem
    chmod 600 ~/.aws/${USER}.pem
    ```

## Accessing the production Puppet templates

Puppet's templates are available in a [production S3 bucket](https://console.aws.amazon.com/s3/home?region=eu-west-1#&bucket=puppet-enterprise-cloudformation-templates&prefix=production/), `s3://puppet-enterprise-cloudformation/production/`.

1.  Download and install an AWS S3 command-line tool, such as [s3cmd](http://s3tools.org/download).

2.  List the available templates in Puppet's S3 bucket.

    ```
    $ s3cmd ls s3://puppet-enterprise-cloudformation-templates/production/

    2017-01-31 11:49      3796   s3://puppet-enterprise-cloudformation-templates/production/ec2instancessample-LATEST.template
    2017-01-31 11:49      3796   s3://puppet-enterprise-cloudformation-templates/production/ec2instancessample-v0.1.0.template
    2017-01-31 11:49      8370   s3://puppet-enterprise-cloudformation-templates/production/elbfromtroposphere-LATEST.template
    2017-01-31 11:49      8370   s3://puppet-enterprise-cloudformation-templates/production/elbfromtroposphere-v0.1.0.template
    2017-01-31 11:49     15330   s3://puppet-enterprise-cloudformation-templates/production/puppet_enterprise_monolithic-LATEST.template
    2017-01-31 11:49     15330   s3://puppet-enterprise-cloudformation-templates/production/puppet_enterprise_monolithic-v0.1.0.template
    ```

3.  Download a suitable template that describes the stack that you want to launch in CloudFormation.

    This example downloads the Puppet Enterprise Monolithic template, which creates a Puppet Master in the default region.

    ```
    $ s3cmd get s3://puppet-enterprise-cloudformation-templates/production/puppet_enterprise_monolithic-LATEST.template
    download: 's3://puppet-enterprise-cloudformation-templates/production/puppet_enterprise_monolithic-LATEST.template' -> './puppet_enterprise_monolithic-LATEST.template'  [1 of 1]
    download: 's3://puppet-enterprise-cloudformation-templates/production/puppet_enterprise_monolithic-LATEST.template' -> './puppet_enterprise_monolithic-LATEST.template'  [1 of 1]
    15330 of 15330   100% in    0s    21.38 kB/s  done
    ```

## Deploying CloudFormation templates

There are two workflows for deploying stacks in AWS CloudFormation: the AWS CLI and the AWS CloudFormation Console.

### Deploying with the AWS CLI

1.  Run the `aws cloudformation create-stack` command, providing your own path to the template (`--template-body`) and settings (`--parameters`).

    ```
    aws cloudformation create-stack --stack-name puppet-enterprise \
      --template-body file:////tmp/puppet_enterprise_monolithic-LATEST.template \
      --parameters ParameterKey=KeyPairName,ParameterValue=exampleuser \
                   ParameterKey=HostedZoneName,ParameterValue=example.com \
                   ParameterKey=PuppetMasterDNSName,ParameterValue=puppet.example.com \
                   ParameterKey=AmiType,ParameterValue=PayAsYouGo \
      --capabilities CAPABILITY_IAM
    ```

    There are two options for the `AmiType` ParameterValue: `PayAsYouGo` and `BringYourOwnLicense`.

    > **Note:** You can specify parameters in a JSON-formatted file. For details, see the [Amazon AWS documentation](https://aws.amazon.com/blogs/devops/passing-parameters-to-cloudformation-stacks-with-the-aws-cli-and-powershell/).

    After successfully launching the stack, the aws CLI provides the StackID.

    ```
    {
        "StackId": "arn:aws:cloudformation:eu-west-1:482693910459:stack/puppet-enterprise/fa7733a0-e96a-11e6-8454-500c3d1abad2"
    }
    ```

2.  Confirm the deployed stack's status with the `aws cloudformation list-stacks` command.

    ```
    $ aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE
    {
    "StackSummaries": [
            {
                "StackId": "arn:aws:cloudformation:eu-west-1:482693910459:stack/puppet-enterprise/fa7733a0-e96a-11e6-8454-500c3d1abad2",
                "StackName": "puppet-enterprise",
                "CreationTime": "2017-02-02T17:13:55.947Z",
                "StackStatus": "CREATE_COMPLETE",
                "TemplateDescription": "A template that will launch a Puppet Enterprise Master"
            }
        ]
    }
    ```

    The `--stack-status-filter` flag can use CREATE_COMPLETE, CREATE_IN_PROGRESS and ROLLBACK_IN_PROGRESS.

3.  Display stack resources using the `aws cloudformation list-stack-resources` command.

    ```
    $ aws cloudformation list-stack-resources --stack-name puppet-enterprise
    {
        "StackResourceSummaries": [
            {
                "ResourceType": "AWS::EC2::Instance",
                "PhysicalResourceId": "i-0429d611fc819106a",
                "LastUpdatedTimestamp": "2017-02-02T17:15:28.637Z",
                "ResourceStatus": "CREATE_COMPLETE",
                "LogicalResourceId": "PuppetMaster"
            },
            {
                "ResourceType": "AWS::Route53::RecordSet",
                "PhysicalResourceId": "puppet.example.com",
                "LastUpdatedTimestamp": "2017-02-02T17:15:58.916Z",
                "ResourceStatus": "CREATE_COMPLETE",
                "LogicalResourceId": "PuppetMasterDNSRecord"
            },
            {
                "ResourceType": "AWS::EC2::SecurityGroup",
                "PhysicalResourceId": "sg-6e012808",
                "LastUpdatedTimestamp": "2017-02-02T17:14:38.594Z",
                "ResourceStatus": "CREATE_COMPLETE",
                "LogicalResourceId": "PuppetMasterSecurityGroup"
            },
            {
                "ResourceType": "AWS::EC2::VPC",
                "PhysicalResourceId": "vpc-ffffd59b",
                "LastUpdatedTimestamp": "2017-02-02T17:14:17.079Z",
                "ResourceStatus": "CREATE_COMPLETE",
                "LogicalResourceId": "VPC"
            }
        ]
    ...
    }
    ```

    This example output includes a subset of the full response.

### Deploying with the AWS CloudFormation Console

To deploy a template in CloudFormation:

1.  Log into the AWS CloudFormation console and open the Stacks tab.

    ![An image of the AWS CloudFormation console's Stacks interface.](./images/awscf_console1.png)

2.  Select Create New Stack.

3.  Provide the Puppet template by uploading a local copy or providing its S3 URL. For example, you can provide the latest monolithic PE template at `https://s3.amazonaws.com/puppet-enterprise-cloudformation/production/puppet_enterprise_monolithic-LATEST.template`.

    ![An image of the AWS CloudFormation console's Create Stack interface, with the "Choose File" button highlighted.](./images/awscf_console2.png)

4.  Provide any necessary configuration parameters. The console automatically provides default values defined in the template.

    ![An image of the AWS CloudFormation console's Specify Details interface with example and default parameters.](./images/awscf_console3.png)

5.  Set any necessary options in the Options interface, such as custom tags on the stack and its resources, or IAM permissions. You must also acknowledge that IAM resources are required to launch the stack.

    ![An image of the AWS CloudFormation console's Options interface, with an acknowledgement that IAM resources are required.](./images/awscf_console4.png)

6.  Review the stack's details, then select Create to deploy the stack.

7.  Confirm the stack's status in the console's Events tab.

    ![An image of the AWS CloudFormation console's Events tab depicting an example Puppet Enterprise CloudFormation deployment.](./images/awscf_console5.png)
