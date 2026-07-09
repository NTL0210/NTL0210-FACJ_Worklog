---
title: "Workshop"
date: 2026-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---
{{% notice warning %}}
⚠️ **Note:** The information below is for reference purposes only. Please **do not copy verbatim** for your report, including this warning.
{{% /notice %}}

# Build an AI Meeting Workforce Platform on AWS

#### Overview

In this workshop, I build **AI Meeting Workforce Platform** — a web platform for managing meetings and follow-up work. Users can sign in, create or view meeting sessions, upload transcripts/audio, and let the system use AI to summarize the meeting, extract action items, and store assigned tasks in role-based dashboards for Admin, Manager, and Employee users.

The architecture is designed with an **AWS managed/serverless-first approach**, which fits the MVP scope of the project:

+ **Frontend:** the Next.js dashboard is built as static files, stored in Amazon S3, and delivered through Amazon CloudFront.
+ **Authentication:** Amazon Cognito manages user sign-up, sign-in, and JWT issuance.
+ **Business API:** Amazon API Gateway receives dashboard requests, validates tokens, and invokes AWS Lambda for business logic.
+ **Database:** Amazon DynamoDB stores users, meetings, tasks, notifications, and processing status.
+ **Meeting storage:** Amazon S3 stores meeting transcripts/audio or bundled meeting data.
+ **AI processing:** EventBridge, Step Functions, and Lambda orchestrate the workflow after new meeting data is uploaded; Lambda calls an external AI service to summarize and extract tasks.
+ **Monitoring:** CloudWatch is used to review logs, track errors, and verify deployment/runtime behavior.

Some components in the architecture diagram, such as VPC, ALB, EC2 Auto Scaling, NAT Gateway, VPC Endpoint, WAF, or SNS, are described as **production extension/design items**. In the report and screenshots, I separate the **MVP components that were implemented** from the **future production architecture** so the workshop stays aligned with the actual project.

#### Content

1. [Workshop overview](5.1-Workshop-overview/)
2. [Prerequisite](5.2-Prerequisite/)
3. [Managed Services (Cognito, DynamoDB, API)](5.3-Managed-Services/)
4. [VPC — Chat & Voice (extension design)](5.4-VPC-Chat-Voice/)
5. [AI Processing Pipeline (S3, EventBridge, Step Functions, Lambda)](5.5-AI-Pipeline/)
6. [Edge & Frontend (S3, CloudFront, WAF)](5.6-Edge-Frontend/)
7. [Monitoring & Cost Optimization](5.7-Monitoring-Cost/)
8. [End-to-end Test](5.8-Test/)
9. [Clean up](5.9-Cleanup/)
