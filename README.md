<!-- note marker start -->
NOTE: This repo contains only the documentation for the private BoltsOps repo code.
Original file: https://github.com/boltops-pro/ecs-asg/blob/master/README.md
The docs are publish so they are available for interested subscribers.
For access to the source code, you can become a BoltOps subscriber.
See: https://learn.boltops.com

<!-- note marker end -->

# ECS ASG Blueprint

[![Watch the video](https://img.boltops.com/boltopspro/video-preview/multiple/ecs-asg.png)](https://youtu.be/8kZUNTTQSGk)

![CodeBuild](https://codebuild.us-west-2.amazonaws.com/badges?uuid=eyJlbmNyeXB0ZWREYXRhIjoiMVNKcW1RbHpnTWhFVGZTQ3pMWW9CUjdUQzJvVG9hTlIxNlhkWDNyZzlkdEtRSFhOS1NFWFVQd0tUVWxVeHgrYW9yTnlxSFJjcEJMcE9YcmtlL2hDTjRRPSIsIml2UGFyYW1ldGVyU3BlYyI6Imd3WnI3aWQrV2NoRVhDMTgiLCJtYXRlcmlhbFNldFNlcmlhbCI6MX0%3D&branch=master)

[![BoltOps Badge](https://img.boltops.com/boltops/badges/boltops-badge.png)](https://www.boltops.com)

This blueprint provisions an AutoScaling on-demand fleet of instances registered to an ECS Cluster.

* Contains [Lambda functions](https://aws.amazon.com/lambda/) for the [Auto Scaling lifecycle hooks](https://docs.aws.amazon.com/autoscaling/ec2/userguide/lifecycle-hooks.html) to call [ECS Container Instance Draining](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/container-instance-draining.html).
* Does a [Auto Scaling Rolling Update](https://aws.amazon.com/premiumsupport/knowledge-center/auto-scaling-group-rolling-updates/) and should result in no downtime as long as your Docker containers are designed to shutdown gracefully.
* Contains [Auto Scaling alarms](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/cloudwatch_alarm_autoscaling.html) based on [ECS Cluster CPU and Memory Metrics](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/cloudwatch-metrics.html). So the on-demand fleet will grow as required to handle more containers. It will also scale down according to save costs.

## Prerequisite

* Setup config/settings.yml: [Settings Setup](https://github.com/boltopspro/reference-architecture/blob/master/docs/settings-setup.md)

## Usage

1. Add blueprint to Gemfile
2. Configure: configs/ecs-asg values
3. Deploy blueprint

## Add

Add the blueprint to your lono project's `Gemfile`.

```ruby
gem "ecs-asg", git: "git@github.com:boltopspro/ecs-asg.git"
```

## Configure

Use the [lono seed](https://lono.cloud/reference/lono-seed/) command to generate a starter config params files. Here are commands for the development and production environments:

    LONO_ENV=development lono seed ecs-asg
    LONO_ENV=production  lono seed ecs-asg

The files in `config/ecs-asg` folder will look something like this:

    configs/ecs-asg/
    └── params
        ├── development.txt
        └── production.txt

There are 2 parameters required: `Subnets` and `Vpc`.  Replace them with your values.  The example starter `configs/ecs-asg/params/development.txt` looks something like this:

    # Required parameters:
    VpcId=vpc-111 # Find at vpc CloudFormation Outputs
    Subnets=subnet-111,subnet-222,subnet-333 # Find at vpc CloudFormation Outputs
    # Optional parameters:
    # InstanceType=m5.large
    # KeyName=...
    # EcsCluster=development
    # ExistingIamInstanceProfile=...
    # ExistingSecurityGroups=...
    # EbsVolumeSize=50
    # MinSize=1
    # MaxSize=4
    # MinInstancesInService=2
    # MaxBatchSize=1


A quick way to get the VPC and subnet values is from the vpc CloudFormation Outputs. Here's an example of development.

![](https://img.boltops.com/boltopspro/blueprints/vpc/dev-vpc-outputs.png)

It is recommended to run the ECS containers on the `PrivateAppSubnets`.

Repeat the same process and configure params and variables files for the `production` environment also.

## Deploy

Use the [lono cfn deploy](http://lono.cloud/reference/lono-cfn-deploy/) command to deploy.:

    LONO_ENV=development lono cfn deploy ecs-asg --sure --no-wait
    LONO_ENV=production  lono cfn deploy ecs-asg --sure --no-wait

If you are using One AWS Account, use these commands instead: [One Account](docs/one-account.md).

## Instance Access

To access the ec2 instances for debugging we strongly recommend using SSM Session Manager.  The instances in this blueprint have SSM manager installed.  You can quickly setup Session Manager on your AWS account with the [Session Manager Pro script](https://github.com/boltopspro/session-manager).

### Security Groups

A security group will be assigned to the ASG EC2 instances. The security group that may be created depends on how you configure the parameters.  Here's how they work:

* If you would like to use a pre-created existing security group then set the `ExistingSecurityGroups` parameter.  The existing security group will be used and no "managed" security group will be created by the CloudFormation template.
* If you're using the managed security group instead, you can control the managed security group rules with the `@security_group_ingress` variable.

### Managed Security Group Rules

To open security group rules on the Managed Security Group you can use the `@security_group_ingress` variable. Example:

configs/ec2/variables/development.rb:

```ruby
@security_group_ingress = [{
  CidrIp: "0.0.0.0/0",
  FromPort: 22,
  IpProtocol: "tcp",
  ToPort: 22,
}]
```

### Custom UserData Script

The UserData can be customized with the `@user_data_script` variable.  The variable should be set to the path of the script. Example:

configs/ec2/variables/development.rb:

```ruby
@user_data_script = "configs/ec2/user_data/bootstrap.sh"
```

The script is wrapped in a [base64](https://lono.cloud/docs/intrinsic-functions/base64/) and [sub](https://lono.cloud/docs/intrinsic-functions/sub/) call. So [Pseudo Parameters](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/pseudo-parameter-reference.html) are available to be used in the script if needed. Example:

configs/ec2/user_data/bootstrap.sh

    echo ${AWS::StackName}

The custom `@user_data_script` is appended to an existing default UserData script that ships with the blueprint. The UserData runs cfn-init and applies configsets before the custom `@user_data_script`.

## Force Rolling Update

You can force a rolling update by setting the `FORCE_UPDATE` environment variable. This adds a comment to the UserData script with a random timestamp. This allows you to deploy new instances without having to "change" code.

## IAM Permissions

The IAM permissions required for this stack are described below.

Service | Description
--- | ---
autoscaling | Creates AutoScaling Group, Policies, Launch Configuration, Lifecycle Hook
cloudformation | To launch the CloudFormation stack.
cloudwatch | Alarm to trigger the AutoScaling Policies.
ec2 | Security Group
iam | Instance Profile associated with AutoScaling Group.
lambda | Lambda function for the AutoScaling Lifecycle Hook.
s3 | Lono managed s3 bucket
sns | SNS Topic notification

## Back to Reference Architecture

That's it. Go back to the main [boltopspro/reference-architecture](https://github.com/boltopspro/reference-architecture/blob/master/README.md)
