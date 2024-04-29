<!-- note marker start -->
NOTE: This repo contains only the documentation for the private BoltsOps repo code.
Original file: https://github.com/boltops-pro/ecs-asg/blob/master/CHANGELOG.md
The docs are publish so they are available for interested subscribers.
For access to the source code, you can become a BoltOps subscriber.
See: https://learn.boltops.com

<!-- note marker end -->

# Change Log

All notable changes to this project will be documented in this file.
This project *loosely tries* to adhere to [Semantic Versioning](http://semver.org/).

## [2.3.1]
- #7 fix alarm metric name and allow alarm thresholds to be configured with params

## [2.3.0]
- change variable to `@iam_policies_override`

## [2.2.4]
- update ECS AMIs

## [2.2.3]
- allow extra iam permissions that decorate: `@iam_managed_policy_arns_extra` and `@iam_policies_extra`

## [2.2.2]
- #6 improve error messaging for lifecyle hooks failure
- update ECS AMIs

## [2.2.1]
- update user_data docs

## [2.2.0]
- fix lifecycle hook with @ecs_cluster variable. AWS changed the ecs container instance ARN, so longer can use that.

## [2.1.1]
- update ECS AMIs

## [2.1.0]
- #5 allow IAM permission customizations
- update ECS AMIs

## [2.0.0]
- #4 upgrade to lono v7
- update ECS AMIs
- update to new recommended AmazonSSMManagedInstanceCore iam role
- use parameter group and conditional parameter
- configsets: cfn-hup, awslogs, ssm
- add CloudWatch logs IAM permission
- use variables for tags and security group: @tags and @security_group_ingress
- allow custom user data script: @user_data_script
- fix lifecycle hook for edge case when ecs clusters have 0 instances

## [1.0.2]
- update ecs ami

## [1.0.1]
- cleanup: ref Conditional true

## [1.0.0]
- #3 lono v6 upgrade: auto_camelize false
- update parameter VpcId

## [0.5.0]
- rename VpcId parameter to Vpc
- add Lono.env to ec2 tag default value
- default value for EcsCluster parameter
- docs: add one-account.md

## [0.4.2]
- update ecs amis

## [0.4.1]
- fix ExistingSecurityGroups typo

## [0.4.0]
- #2 upgrade lono v5.2
- use camelize naming

## [0.3.2]
- update default tag name

## [0.3.1]
- update default instance type to m5.large

## [0.3.0]
- #1 Lifecycle hook upgrades

## [0.2.0]
- fix asg\_security\_group\_default condition

## [0.1.0]
- Initial release
