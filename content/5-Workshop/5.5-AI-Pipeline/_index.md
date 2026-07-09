---
title : "AI Processing Pipeline"
date : 2026-01-01 
weight : 5 
chapter : false
pre : " <b> 5.5. </b> "
---

#### From meeting bundle to assigned tasks

When new transcript/audio data is available for a meeting, it is stored as an **S3 bundle**. That upload triggers an event-driven pipeline that calls an external AI to summarize and extract tasks, saves the tasks in DynamoDB, and displays or notifies the assignees.

1. [S3 bundle & EventBridge trigger](5.5.1-s3-eventbridge/)
2. [Step Functions & the AI Lambdas](5.5.2-stepfunctions-ai/)
3. [Save tasks & notify with SNS](5.5.3-notify-sns/)
