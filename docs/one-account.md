<!-- note marker start -->
**NOTE**: This repo contains only the documentation for the private BoltsOps Pro repo code.
Original file: https://github.com/boltopspro/ecs-asg/blob/master/docs/one-account.md
The docs are publish so they are available for interested customers.
For access to the source code, you must be a paying BoltOps Pro subscriber.
If are interested, you can contact us at contact@boltops.com or https://www.boltops.com

<!-- note marker end -->

## One AWS Account Commands

[![Watch the video](https://img.boltops.com/boltopspro/video-preview/single/ecs-asg.png)](https://www.youtube.com/watch?v=0MNtp47gaIg)

Here are the summarized commands if you're using the One AWS account strategy.

## Deploy

Let's deploy now with [lono deploy](https://lono.cloud/reference/lono-cfn-deploy/).

    LONO_ENV=development lono cfn deploy ecs-asg-development --blueprint ecs-asg --sure --no-wait
    LONO_ENV=production  lono cfn deploy ecs-asg-production  --blueprint ecs-asg --sure --no-wait
