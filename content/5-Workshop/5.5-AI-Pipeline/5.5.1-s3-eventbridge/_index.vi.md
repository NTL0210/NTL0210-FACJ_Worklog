---
title : "S3 Bundle & EventBridge"
date : 2026-01-01 
weight : 1 
chapter : false
pre : " <b> 5.5.1. </b> "
---

#### Tạo bucket bundle và trigger sự kiện

1. Tạo S3 bucket `ai-meeting-workforce-audio` (region ap-southeast-1, chặn public access).
2. Chọn cách đặt key, ví dụ `uploads/<fileName>` cho file upload ban đầu và `meetings/<meetingId>/...` cho dữ liệu theo từng cuộc họp.
3. Upload thử một file audio/transcript để kiểm tra bucket nhận dữ liệu đúng.
4. Trong **EventBridge → Rules**, tạo rule khớp sự kiện **S3 Object Created** cho bucket `ai-meeting-workforce-audio`, prefix `uploads/`, và đặt target là state machine Step Functions.

![uploaded object](/images/5-Workshop/5.5-AI-Pipeline/5.5.1-s3-eventbridge/uploaded-object.png)

![s3 eventbridge](/images/5-Workshop/5.5-AI-Pipeline/5.5.1-s3-eventbridge/s3-eventbridge.png)

{{% notice note %}}
Bundle có thể được ghi theo hai cách: tầng EC2 ghi trực tiếp, hoặc dashboard upload audio qua API (bước 9). Dù cách nào, sự kiện **Object Created** chính là thứ khởi động pipeline.
{{% /notice %}}
