---
title : "Save Tasks & Notify (SNS)"
date : 2026-01-01 
weight : 3 
chapter : false
pre : " <b> 5.5.3. </b> "
---

#### Notify the assignees

1. Create an SNS topic for pipeline notifications.
2. Add subscriptions — for the lab, an **email** subscription (confirm it from your inbox). In production you would send targeted notifications per assignee.
3. In the **Task Lambda** (or a final Step Functions state), after saving tasks, **publish** a message to `ai-meeting-notify` summarizing who was assigned what (step 16).
4. Grant the Lambda `sns:Publish` on that topic.

![sns notify](/images/5-Workshop/5.5-AI-Pipeline/5.5.3-notify-sns/sns.png)

{{% notice note %}}
End-to-end, the pipeline now is: **S3 bundle → EventBridge → Step Functions → AI → DynamoDB (Task) → SNS → user**. You will test the whole chain in section 5.8.
{{% /notice %}}
