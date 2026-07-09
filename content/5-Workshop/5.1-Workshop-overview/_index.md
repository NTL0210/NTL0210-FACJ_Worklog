---
title : "Introduction"
date : 2026-01-01 
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---

#### What you will build

This workshop implements an MVP for **AI Meeting Workforce Platform**. The main goal is to turn meeting data into actionable information: transcripts/audio are stored, AI summarizes the meeting, extracts tasks, and the dashboard displays those tasks by user role and processing status.

The platform is organized into the following service groups:

+ **Frontend & Edge:** the Next.js dashboard is built as a static site, uploaded to Amazon S3, and delivered with Amazon CloudFront. AWS WAF can be added as an edge protection layer for a production deployment.
+ **Authentication:** Amazon Cognito manages users, sign-in, and JWTs. The dashboard sends this token when calling protected APIs.
+ **Business API:** Amazon API Gateway is the backend entry point; AWS Lambda handles actions such as loading user information, creating meetings, saving processing status, and returning dashboard data.
+ **Data layer:** Amazon DynamoDB stores core project data such as users, meetings, tasks, notifications, and processing status.
+ **Meeting storage:** Amazon S3 stores transcripts, audio files, or meeting bundles using a traceable structure such as `meetings/<meetingId>/`.
+ **AI pipeline:** when new meeting data is uploaded, EventBridge starts Step Functions. Step Functions invokes Lambda to read files from S3, call the external AI service, receive the summary/task list, and write the result back to DynamoDB.
+ **Monitoring & cost control:** CloudWatch is used to review Lambda/API/pipeline logs, track errors, and debug deployment issues. AWS Budgets or billing alarms help avoid unexpected costs.

Components such as VPC, ALB, EC2 Auto Scaling, NAT Gateway, and DynamoDB VPC Endpoint in the architecture diagram are extension items for a production scenario where the Chat & Voice service needs to run continuously in real time. If those components are not deployed in the AWS account, they should be described as **extension design** rather than completed implementation.

![architecture](/images/5-Workshop/5.1-Workshop-overview/architecture.png)

#### MVP end-to-end flow

1. The user opens the dashboard through **CloudFront** and signs in with **Cognito**.
2. The dashboard receives a JWT from Cognito and calls the backend through **API Gateway**.
3. **Lambda** handles business logic and reads/writes users, meetings, and tasks in **DynamoDB**.
4. The user uploads a transcript/audio file, or the system creates a meeting bundle in **S3**.
5. The S3 upload event triggers **EventBridge → Step Functions**.
6. Step Functions invokes the **AI processing Lambda** to read the data, call the external AI service, and receive a summary plus extracted tasks.
7. Lambda writes tasks to **DynamoDB**, and the dashboard displays the result to users based on their roles.
8. **CloudWatch** records logs for runtime verification, debugging, and deployment evidence.

{{% notice info %}}
The main workshop region should be **Asia Pacific (Singapore) — ap-southeast-1**. CloudFront is a global service, and WAF associated with CloudFront must be created with the **Global (CloudFront)** scope.
{{% /notice %}}

#### Screenshots to capture

| Evidence area | AWS screenshot to capture | Purpose |
|---|---|---|
| Overall architecture | Project architecture diagram | Shows the full system flow |
| Sign-in | Cognito User Pool and App client | Proves the authentication layer exists |
| Database | DynamoDB tables and a few sample items | Proves users/meetings/tasks are stored |
| Storage | S3 bucket containing transcripts/audio or meeting bundles | Proves meeting data is stored |
| Backend API | API Gateway routes and Lambda integration | Proves the dashboard calls the backend through API |
| AI processing | Step Functions state machine and a successful execution | Proves the AI pipeline can run |
| AI result | Task item written to DynamoDB | Proves AI output becomes business data |
| Frontend | CloudFront distribution with `Deployed` status and dashboard URL | Proves the frontend is delivered |
| Monitoring | CloudWatch log group/log stream for Lambda or Step Functions | Proves logs are available for debugging and operations |
| Final test | Dashboard after sign-in and the task list page | Proves the end-to-end flow works |
