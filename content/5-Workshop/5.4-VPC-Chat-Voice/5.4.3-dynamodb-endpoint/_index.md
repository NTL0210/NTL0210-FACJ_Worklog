---
title : "DynamoDB VPC Endpoint"
date : 2026-01-01 
weight : 3 
chapter : false
pre : " <b> 5.4.3. </b> "
---

#### Access DynamoDB privately through a VPC endpoint

To let resources in private subnets access DynamoDB without traversing the public Internet, create a **Gateway VPC Endpoint** for DynamoDB and attach it to the private route tables. In the current setup, the VPC also has a Gateway Endpoint for S3 to support bucket access from private subnets.

1. Open **VPC console** → **Endpoints** → **Create endpoint**.
2. Service: select `com.amazonaws.ap-southeast-1.dynamodb`.
3. Endpoint type: **Gateway**.
4. VPC: `app-vpc`.
5. Route tables: select the private route tables, for example `rt-private-a` and `rt-private-b`.
6. Create the endpoint and verify that the state is **Available**.

![dynamodb endpoint](/images/5-Workshop/5.4-VPC-Chat-Voice/5.4.3-dynamodb-endpoint/endpoint.png)

{{% notice note %}}
A DynamoDB Gateway Endpoint is different from an Interface Endpoint: it is attached to route tables and does not create ENIs in subnets. This is the appropriate and cost-efficient option for DynamoDB/S3 access from a VPC.
{{% /notice %}}
