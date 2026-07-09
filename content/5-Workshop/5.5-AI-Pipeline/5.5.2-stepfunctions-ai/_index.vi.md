---
title : "Step Functions & Lambda AI"
date : 2026-01-01 
weight : 2 
chapter : false
pre : " <b> 5.5.2. </b> "
---

#### Điều phối xử lý AI

1. **Lưu API key AI** trong **AWS Secrets Manager** (ví dụ `ai-meeting/ai-api-key`). Tuyệt đối không hard-code.
2. **Tạo (các) Lambda xử lý:**
   + **Speech Processing / Call-model Lambda** — đọc bundle từ S3 và gọi **AI API đa phương thức bên ngoài** (Gemini / OpenAI / Claude) để tạo bản gỡ băng và danh sách công việc (bước 12–14). Theo hướng bundle, một lần gọi multimodal trả về cả Speech-to-Text lẫn công việc.
   + **Task Lambda** — nhận danh sách công việc và ghi vào `ai-meeting-task` (bước 15).
   + Cấp cho mỗi Lambda đúng quyền cần thiết (đọc S3, đọc Secrets Manager, ghi DynamoDB, publish SNS).

![save lambda](/images/5-Workshop/5.5-AI-Pipeline/5.5.2-stepfunctions-ai/save-lambda.png)

3. **Tạo state machine Step Functions** (Standard) chạy: *Đọc bundle → Gọi AI → Lưu công việc → Thông báo*. Thêm khối **Retry** và **Catch** để lỗi AI tạm thời được thử lại và lỗi thật đi vào nhánh dead-letter.
4. Đặt state machine này làm **target của EventBridge rule** ở 5.5.1.

![step functions](/images/5-Workshop/5.5-AI-Pipeline/5.5.2-stepfunctions-ai/state-machine.png)

{{% notice tip %}}
Để kiểm soát chi phí AI, giới hạn max output token, và cân nhắc batch hoặc model nhẹ hơn cho các cuộc họp đơn giản. Phần lớn chi phí AI theo mỗi cuộc họp phát sinh ở đây.
{{% /notice %}}
