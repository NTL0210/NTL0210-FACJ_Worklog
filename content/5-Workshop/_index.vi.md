---
title: "Workshop"
date: 2026-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---
{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

# Xây dựng nền tảng AI Meeting Workforce trên AWS

#### Tổng quan

Trong workshop này, em xây dựng **AI Meeting Workforce Platform** — một nền tảng web hỗ trợ quản lý cuộc họp và công việc sau cuộc họp. Người dùng có thể đăng nhập, tạo hoặc xem phiên họp, upload transcript/audio, sau đó hệ thống dùng AI để tóm tắt nội dung, trích xuất action items và lưu các task vào dashboard theo vai trò như Admin, Manager và Employee.

Kiến trúc được thiết kế theo hướng **AWS managed/serverless trước**, phù hợp với phạm vi MVP của dự án:

+ **Frontend:** giao diện Next.js được build thành static files, lưu trên Amazon S3 và phân phối qua Amazon CloudFront.
+ **Authentication:** Amazon Cognito quản lý đăng ký, đăng nhập và phát JWT cho người dùng.
+ **Business API:** Amazon API Gateway nhận request từ dashboard, kiểm tra token và gọi AWS Lambda để xử lý nghiệp vụ.
+ **Database:** Amazon DynamoDB lưu dữ liệu người dùng, cuộc họp, task, notification và trạng thái xử lý.
+ **Meeting storage:** Amazon S3 lưu transcript/audio hoặc bundle dữ liệu của cuộc họp.
+ **AI processing:** EventBridge, Step Functions và Lambda điều phối luồng xử lý sau khi có dữ liệu cuộc họp mới; Lambda gọi AI bên ngoài để tóm tắt và trích xuất task.
+ **Monitoring:** CloudWatch dùng để xem log, theo dõi lỗi và kiểm tra quá trình deploy/thực thi.

Một số thành phần trong sơ đồ kiến trúc như VPC, ALB, EC2 Auto Scaling, NAT Gateway, VPC Endpoint, WAF hoặc SNS được trình bày như **hướng mở rộng/thiết kế production**. Khi viết báo cáo và chụp ảnh minh chứng, em tách rõ phần **đã triển khai trong MVP** và phần **định hướng mở rộng** để nội dung khớp với dự án thực tế.

#### Nội dung

1. [Tổng quan workshop](5.1-Workshop-overview/)
2. [Chuẩn bị](5.2-Prerequisite/)
3. [Dịch vụ Managed (Cognito, DynamoDB, API)](5.3-Managed-Services/)
4. [VPC — Chat & Voice (thiết kế mở rộng)](5.4-VPC-Chat-Voice/)
5. [Pipeline xử lý AI (S3, EventBridge, Step Functions, Lambda)](5.5-AI-Pipeline/)
6. [Edge & Frontend (S3, CloudFront, WAF)](5.6-Edge-Frontend/)
7. [Giám sát & Tối ưu chi phí](5.7-Monitoring-Cost/)
8. [Kiểm thử end-to-end](5.8-Test/)
9. [Dọn dẹp tài nguyên](5.9-Cleanup/)
