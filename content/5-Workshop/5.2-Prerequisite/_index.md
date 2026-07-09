---
title : "Prerequisite"
date : 2026-01-01 
weight : 2 
chapter : false
pre : " <b> 5.2. </b> "
---

#### Before you begin

Prepare the following before building the AI Meeting Workforce platform.

**Account & Region**
+ An AWS account with permissions to create the core MVP services: IAM, Lambda, API Gateway, DynamoDB, S3, EventBridge, Step Functions, Cognito, CloudFront, and CloudWatch.
+ If you also implement the production/real-time Chat & Voice architecture, the account needs additional permissions for VPC, EC2, Elastic Load Balancing, Auto Scaling, NAT Gateway, VPC Endpoint, WAF, and SNS.
+ Create regional resources in **Asia Pacific (Singapore) — ap-southeast-1**. CloudFront is a global service.

![region](/images/5-Workshop/5.2-Prerequisite/region.png)

**Local tools**
+ **AWS CLI v2** (configured with `aws configure`).
+ **Node.js 18+** and **npm** to build the Next.js dashboard and deploy Lambda if Node.js is used.
+ **AWS CDK v2** (`npm install -g aws-cdk`) if you deploy infrastructure as code; if CDK is not used, you can follow the AWS Console steps and capture screenshots for each service.
+ Git for source control and change tracking.

**External services**
+ An **AI API key** (Gemini / OpenAI / Claude or an equivalent service) for meeting summarization and task extraction. Store the key in **AWS Secrets Manager** or protected environment variables, not directly in source code.

**Naming convention (suggested)**
+ Use a consistent prefix such as `ai-meeting-` or `ai-workforce-` for resources, for example `ai-meeting-api`, `ai-meeting-bundle`, `ai-meeting-task`.
+ When capturing screenshots, keep resource names clear so readers can map each AWS resource to the architecture.

{{% notice tip %}}
Create a **billing alarm** before provisioning resources. For the MVP, cost mainly comes from requests, logs, S3 storage, and CloudFront. For the VPC/EC2/NAT/ALB extension, hourly cost is much higher, so create those resources only when you need evidence screenshots or real testing.
{{% /notice %}}
