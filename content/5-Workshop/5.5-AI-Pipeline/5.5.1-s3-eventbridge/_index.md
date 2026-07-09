---
title : "S3 Bundle & EventBridge"
date : 2026-01-01 
weight : 1 
chapter : false
pre : " <b> 5.5.1. </b> "
---

#### Create the bundle bucket and event trigger

1. Create the S3 bucket `ai-meeting-workforce-audio` (region ap-southeast-1, block public access).
2. Decide a key layout, for example `uploads/<fileName>` for initial uploads and `meetings/<meetingId>/...` for per-meeting data.
3. Upload a test audio/transcript file to verify that the bucket receives data correctly.
4. In **EventBridge → Rules**, create a rule that matches **S3 Object Created** events for the `ai-meeting-workforce-audio` bucket with the `uploads/` prefix, and set the target to the Step Functions state machine.

![uploaded object](/images/5-Workshop/5.5-AI-Pipeline/5.5.1-s3-eventbridge/uploaded-object.png)

![s3 eventbridge](/images/5-Workshop/5.5-AI-Pipeline/5.5.1-s3-eventbridge/s3-eventbridge.png)

{{% notice note %}}
The bundle can be written two ways: the EC2 tier writes it directly, or the dashboard uploads the audio via the API (step 9). Either way, the **Object Created** event is what starts the pipeline.
{{% /notice %}}
