---
title : "VPC — Chat & Voice"
date : 2026-01-01 
weight : 4 
chapter : false
pre : " <b> 5.4. </b> "
---

#### Thiết kế mở rộng cho tầng realtime

Trong MVP hiện tại, trọng tâm của dự án là dashboard, lưu transcript/audio, xử lý AI và quản lý task. Phần **VPC — Chat & Voice** là thiết kế mở rộng nếu hệ thống cần một dịch vụ realtime luôn chạy, ví dụ phòng họp voice/chat trực tiếp, WebSocket server hoặc media service riêng.

Khi triển khai production, tầng này có thể chạy trên **EC2 trong private subnet**, đặt sau **Application Load Balancer**, có **Auto Scaling group** để tự phục hồi, và dùng **DynamoDB VPC Endpoint** để ghi chat session mà không đi qua Internet công cộng.

Các bước thiết kế gồm:

1. [Mạng VPC — subnet, IGW, NAT Gateway](5.4.1-vpc-network/)
2. [ALB, Auto Scaling group & EC2](5.4.2-alb-asg-ec2/)
3. [DynamoDB Interface Endpoint](5.4.3-dynamodb-endpoint/)

{{% notice info %}}
Nếu chưa tạo VPC/ALB/EC2 trong tài khoản AWS, hãy trình bày phần này là **production extension**. Ảnh bắt buộc cho MVP nên ưu tiên Cognito, S3, DynamoDB, Lambda, API Gateway, Step Functions, CloudFront và CloudWatch.
{{% /notice %}}
