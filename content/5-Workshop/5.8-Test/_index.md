---
title : "End-to-end Test"
date : 2026-01-01 
weight : 8 
chapter : false
pre : " <b> 5.8. </b> "
---

#### Test the whole platform

Walk through the MVP user journey and confirm each stage with screenshots.

1. **Sign up / sign in:** open the CloudFront URL or the current dashboard deployment URL, sign up/sign in with **Cognito**, then capture the dashboard after authentication.

![login](/images/5-Workshop/5.8-Test/login.png)

2. **Business API:** perform an action in the dashboard, such as creating a meeting or viewing the task list. Verify the request flows through **API Gateway → Lambda** and data is read/written in **DynamoDB**.

![dashboard](/images/5-Workshop/5.8-Test/dashboard.png)

3. **Meeting data storage:** upload a transcript/audio file or create a meeting bundle in **S3**. Capture the bucket and object path for that meeting.

![workspace](/images/5-Workshop/5.8-Test/workspace.png)

![create workspace](/images/5-Workshop/5.8-Test/create_workspace.png)

4. **AI pipeline:** confirm the S3 upload triggers **EventBridge → Step Functions**. Capture the state machine and one successful execution.
5. **Processing result:** verify the summary/task list is written to **DynamoDB**. Capture the task item or task table after processing.

![ai meeting](/images/5-Workshop/5.8-Test/AI_meeting.png)

![ai meeting result](/images/5-Workshop/5.8-Test/Ai_meeting01.png)

![ai meeting tasks](/images/5-Workshop/5.8-Test/AI_meeting02.png)

6. **Monitoring:** open **CloudWatch Logs** for Lambda/API/pipeline to prove there are runtime logs and debugging evidence.

![cloudwatch test](/images/5-Workshop/5.8-Test/checkCloudWatch.png)

7. **Extension layer (optional):** if the VPC/ALB/EC2 real-time layer is deployed, capture the healthy target group, ASG instance, and chat record saved through the DynamoDB endpoint. If not deployed, label it as a future extension.
8. **Notification (optional):** if SNS or in-dashboard notification is included, capture the notification and the related status data in DynamoDB.

{{% notice tip %}}
If the pipeline does not start, check in order: whether the file actually arrived in S3, whether the EventBridge rule matches the bucket/object event, whether the Step Functions role can invoke Lambda, and what the Lambda CloudWatch Logs report.
{{% /notice %}}
