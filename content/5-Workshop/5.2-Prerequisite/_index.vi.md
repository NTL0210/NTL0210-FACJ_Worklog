---
title : "Chuẩn bị"
date : 2026-01-01 
weight : 2 
chapter : false
pre : " <b> 5.2. </b> "
---

#### Trước khi bắt đầu

Chuẩn bị các mục sau trước khi xây dựng nền tảng AI Meeting Workforce.

**Tài khoản & Region**
+ Một tài khoản AWS có quyền tạo các dịch vụ chính của MVP: IAM, Lambda, API Gateway, DynamoDB, S3, EventBridge, Step Functions, Cognito, CloudFront và CloudWatch.
+ Nếu triển khai thêm kiến trúc production/Chat & Voice realtime, tài khoản cần thêm quyền cho VPC, EC2, Elastic Load Balancing, Auto Scaling, NAT Gateway, VPC Endpoint, WAF và SNS.
+ Thực hiện các tài nguyên theo region tại **Asia Pacific (Singapore) — ap-southeast-1**. CloudFront là dịch vụ global.

![region](/images/5-Workshop/5.2-Prerequisite/region.png)

**Công cụ máy cá nhân**
+ **AWS CLI v2** (đã cấu hình bằng `aws configure`).
+ **Node.js 18+** và **npm** để build dashboard Next.js và deploy Lambda nếu dùng Node.js.
+ **AWS CDK v2** (`npm install -g aws-cdk`) nếu triển khai hạ tầng bằng code; nếu chưa dùng CDK, có thể làm theo AWS Console và chụp ảnh từng bước.
+ Git để quản lý source code và lưu lại thay đổi.

**Dịch vụ bên ngoài**
+ Một **API key AI** (Gemini / OpenAI / Claude hoặc dịch vụ tương đương) cho phần tóm tắt meeting và trích xuất task. Nên lưu key trong **AWS Secrets Manager** hoặc biến môi trường được bảo vệ, không hard-code trong source code.

**Quy ước đặt tên (gợi ý)**
+ Dùng tiền tố nhất quán như `ai-meeting-` hoặc `ai-workforce-` cho mọi tài nguyên, ví dụ `ai-meeting-api`, `ai-meeting-bundle`, `ai-meeting-task`.
+ Khi chụp ảnh minh chứng, nên giữ tên tài nguyên rõ ràng để người xem biết tài nguyên đó thuộc phần nào của kiến trúc.

{{% notice tip %}}
Hãy đặt **billing alarm** trước khi tạo tài nguyên. Với MVP, chi phí chủ yếu đến từ request, log, S3 storage và CloudFront. Với phần mở rộng VPC/EC2/NAT/ALB, chi phí tính theo giờ cao hơn nhiều, nên chỉ tạo khi thật sự cần chụp minh chứng hoặc kiểm thử.
{{% /notice %}}
