---
title : "Monitoring & Cost"
date : 2026-01-01 
weight : 7 
chapter : false
pre : " <b> 5.7. </b> "
---

#### Monitoring and cost optimization

**Centralized monitoring for the MVP**
1. Confirm **Lambda**, **Step Functions**, **API Gateway**, and the AI processing pipeline all write logs to **CloudWatch Logs**.
2. Review key metrics: API invocation count, Lambda errors, Lambda duration, Step Functions execution status, and DynamoDB read/write errors.
3. Create a simple **CloudWatch dashboard** to monitor the backend API and AI pipeline.
4. Create **alarms** for important failures, such as Lambda `Errors`, Step Functions `ExecutionsFailed`, or API Gateway 5xx responses.

**Monitoring for the production extension**
If the VPC/ALB/EC2 real-time layer is deployed, add metrics such as ALB 5xx, unhealthy targets, EC2 CPU, ASG in-service instance count, and NAT Gateway bytes. These metrics are not required for the MVP if the real-time layer has not been deployed.

**S3 storage lifecycle**
1. On the meeting bundle bucket, for example `ai-meeting-bundle`, add an **S3 Lifecycle rule** to transition older transcripts/audio to a cheaper storage class after a period such as 30 days.
2. For data kept only for audit or long-term reference, expire it or transition it to Glacier/Deep Archive after 90-120 days depending on project needs.

**Cost controls**
+ Prefer a lighter AI model or limit output tokens when quality is still acceptable.
+ DynamoDB on-demand and Lambda fit the MVP because they are pay-per-use.
+ Set CloudWatch Logs retention appropriately, such as 7-30 days during the demo phase.
+ For the production extension, EC2, ALB, NAT Gateway, and VPC Endpoint are billed hourly, so create them only when real testing is needed and clean them up after capturing screenshots.

![lambda logs](/images/5-Workshop/5.7-Monitoring-Cost/lambda-logs.png)

{{% notice tip %}}
For evidence, capture CloudWatch Logs or a CloudWatch Dashboard. If a dashboard has not been created, a Lambda log group/log stream or a Step Functions execution view is enough to prove that the system can be monitored and debugged.
{{% /notice %}}
