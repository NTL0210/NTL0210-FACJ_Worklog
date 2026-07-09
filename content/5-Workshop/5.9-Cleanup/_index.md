---
title : "Clean up"
date : 2026-01-01 
weight : 9 
chapter : false
pre : " <b> 5.9. </b> "
---

#### Clean up resources

Delete in roughly reverse order of creation to avoid dependency errors. If you deployed with CDK, run `cdk destroy` per stack instead.

1. **Edge:** disable and delete the **CloudFront** distribution; delete the **WAF** Web ACL (us-east-1). Empty and delete the `ai-meeting-frontend` bucket.
2. **AI pipeline:** delete the **Step Functions** state machine, the **EventBridge** rule, and the pipeline **Lambdas**. Empty and delete the `ai-meeting-bundle` bucket (and any Glacier objects). Delete the **SNS** topics.
3. **VPC / Chat & Voice:** delete the **ALB** and target group, delete the **Auto Scaling group** (terminates EC2), delete the **launch template**, delete the **DynamoDB Interface Endpoints**, delete the **NAT Gateways**, release their **Elastic IPs**, then delete the **VPC** (removes subnets, route tables, IGW).
4. **Managed:** delete the **API Gateway** API, the **API Lambda**, the three **DynamoDB** tables, and the **Cognito** user pool.
5. **Secrets & logs:** delete the **Secrets Manager** secret and any **CloudWatch** log groups/dashboards/alarms you no longer need.
6. **Verify** the Billing/Cost console shows the hourly resources (EC2, NAT, ALB, endpoints) are gone.

{{% notice warning %}}
The most expensive leftovers are **NAT Gateways, their Elastic IPs, the ALB, EC2 instances, and interface endpoints** — double-check these are all deleted, as they bill hourly even when idle.
{{% /notice %}}
