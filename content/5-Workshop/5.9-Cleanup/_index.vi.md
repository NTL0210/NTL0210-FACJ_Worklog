---
title : "Dọn dẹp tài nguyên"
date : 2026-01-01 
weight : 9 
chapter : false
pre : " <b> 5.9. </b> "
---

#### Dọn dẹp tài nguyên

Xóa theo thứ tự gần như ngược với lúc tạo để tránh lỗi phụ thuộc. Nếu bạn triển khai bằng CDK, hãy chạy `cdk destroy` cho từng stack.

1. **Edge:** vô hiệu hóa và xóa **CloudFront** distribution; xóa **WAF** Web ACL (us-east-1). Làm rỗng và xóa bucket `ai-meeting-frontend`.
2. **Pipeline AI:** xóa **Step Functions** state machine, **EventBridge** rule, và các **Lambda** của pipeline. Làm rỗng và xóa bucket `ai-meeting-bundle` (và các object Glacier). Xóa các **SNS** topic.
3. **VPC / Chat & Voice:** xóa **ALB** và target group, xóa **Auto Scaling group** (sẽ tắt EC2), xóa **launch template**, xóa các **DynamoDB Interface Endpoint**, xóa các **NAT Gateway**, release **Elastic IP** của chúng, rồi xóa **VPC** (xóa luôn subnet, route table, IGW).
4. **Managed:** xóa **API** trong API Gateway, **API Lambda**, ba bảng **DynamoDB**, và **Cognito** user pool.
5. **Secrets & logs:** xóa secret trong **Secrets Manager** và các log group/dashboard/alarm **CloudWatch** không còn cần.
6. **Kiểm tra** trong Billing/Cost console rằng các tài nguyên tính theo giờ (EC2, NAT, ALB, endpoint) đã hết.

{{% notice warning %}}
Những thứ tốn kém nhất nếu bỏ sót là **NAT Gateway, Elastic IP của chúng, ALB, EC2 instance và interface endpoint** — hãy kiểm tra kỹ đã xóa hết, vì chúng tính phí theo giờ ngay cả khi rảnh.
{{% /notice %}}
