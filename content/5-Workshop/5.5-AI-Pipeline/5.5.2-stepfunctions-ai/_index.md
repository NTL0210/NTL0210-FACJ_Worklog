---
title : "Step Functions & AI Lambdas"
date : 2026-01-01 
weight : 2 
chapter : false
pre : " <b> 5.5.2. </b> "
---

#### Orchestrate the AI processing

1. **Store the AI key** in **AWS Secrets Manager** (for example `ai-meeting/ai-api-key`). Never hard-code it.
2. **Create the processing Lambda(s):**
   + **Speech Processing / Call-model Lambda** — reads the bundle from S3 and calls the **external multimodal AI API** (Gemini / OpenAI / Claude) to produce the transcript and the extracted task list (steps 12–14). In the bundle approach, one multimodal call returns both Speech-to-Text and tasks.
   + **Task Lambda** — receives the task list and writes it to `ai-meeting-task` (step 15).
   + Grant each Lambda only the permissions it needs (S3 read, Secrets Manager read, DynamoDB write, SNS publish).

![save lambda](/images/5-Workshop/5.5-AI-Pipeline/5.5.2-stepfunctions-ai/save-lambda.png)

3. **Create the Step Functions state machine** (Standard) that runs: *Read bundle → Call AI → Save tasks → Notify*. Add **Retry** and **Catch** blocks so transient AI errors are retried and failures go to a dead-letter path.
4. Set this state machine as the **target of the EventBridge rule** from 5.5.1.

![step functions](/images/5-Workshop/5.5-AI-Pipeline/5.5.2-stepfunctions-ai/state-machine.png)

{{% notice tip %}}
To control AI cost, cap the max output tokens, and consider batching or a lighter model for simple meetings. This is where most of the per-meeting AI cost is incurred.
{{% /notice %}}
