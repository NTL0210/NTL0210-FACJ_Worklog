---
title : "Lưu công việc & Thông báo (SNS)"
date : 2026-01-01 
weight : 3 
chapter : false
pre : " <b> 5.5.3. </b> "
---

#### Thông báo cho người được giao

1. Tạo SNS topic dùng cho thông báo pipeline.
2. Thêm subscription — với lab, dùng subscription **email** (xác nhận từ hộp thư). Ở production bạn sẽ gửi thông báo riêng cho từng người được giao.
3. Trong **Task Lambda** (hoặc một state cuối của Step Functions), sau khi lưu công việc, **publish** một message tới `ai-meeting-notify` tóm tắt ai được giao việc gì (bước 16).
4. Cấp cho Lambda quyền `sns:Publish` trên topic đó.

![sns notify](/images/5-Workshop/5.5-AI-Pipeline/5.5.3-notify-sns/sns.png)

{{% notice note %}}
Toàn trình, pipeline bây giờ là: **S3 bundle → EventBridge → Step Functions → AI → DynamoDB (Task) → SNS → người dùng**. Bạn sẽ kiểm thử toàn bộ chuỗi ở phần 5.8.
{{% /notice %}}
