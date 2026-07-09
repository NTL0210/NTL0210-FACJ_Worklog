---
title : "VPC — Chat & Voice"
date : 2026-01-01 
weight : 4 
chapter : false
pre : " <b> 5.4. </b> "
---

#### Extension design for the real-time layer

In the current MVP, the project focuses on the dashboard, transcript/audio storage, AI processing, and task management. The **VPC — Chat & Voice** section is an extension design for cases where the system needs an always-on real-time service, such as live voice/chat rooms, a WebSocket server, or a dedicated media service.

In a production deployment, this layer can run on **EC2 inside private subnets**, sit behind an **Application Load Balancer**, use an **Auto Scaling group** for self-healing, and use a **DynamoDB VPC Endpoint** to write chat sessions without traversing the public Internet.

The design steps are:

1. [VPC network — subnets, IGW, NAT Gateways](5.4.1-vpc-network/)
2. [ALB, Auto Scaling group & EC2](5.4.2-alb-asg-ec2/)
3. [DynamoDB Interface Endpoint](5.4.3-dynamodb-endpoint/)

{{% notice info %}}
If VPC/ALB/EC2 resources have not been created in the AWS account, describe this section as a **production extension**. For MVP evidence, prioritize Cognito, S3, DynamoDB, Lambda, API Gateway, Step Functions, CloudFront, and CloudWatch screenshots.
{{% /notice %}}
