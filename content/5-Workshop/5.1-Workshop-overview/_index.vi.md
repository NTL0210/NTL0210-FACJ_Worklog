---
title : "Giới thiệu"
date : 2026-01-01 
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---

#### Bạn sẽ xây dựng gì

Workshop triển khai một MVP cho **AI Meeting Workforce Platform**. Mục tiêu chính là biến dữ liệu cuộc họp thành thông tin có thể hành động: transcript/audio được lưu lại, AI tóm tắt nội dung, trích xuất task, sau đó dashboard hiển thị task theo vai trò và trạng thái xử lý.

Nền tảng được chia thành các nhóm dịch vụ sau:

+ **Frontend & Edge:** giao diện Next.js được build thành static site, upload lên Amazon S3 và phân phối bằng Amazon CloudFront. AWS WAF là lớp bảo vệ có thể thêm khi triển khai production.
+ **Authentication:** Amazon Cognito quản lý người dùng, đăng nhập và JWT. Token này được dashboard gửi kèm khi gọi API.
+ **Business API:** Amazon API Gateway là điểm vào của backend; AWS Lambda xử lý các thao tác như lấy thông tin người dùng, tạo phiên họp, lưu trạng thái xử lý và trả dữ liệu cho dashboard.
+ **Data layer:** Amazon DynamoDB lưu dữ liệu chính của dự án như users, meetings, tasks, notifications và processing status.
+ **Meeting storage:** Amazon S3 lưu transcript, audio hoặc file bundle của từng cuộc họp theo cấu trúc dễ truy vết, ví dụ `meetings/<meetingId>/`.
+ **AI pipeline:** khi có dữ liệu cuộc họp mới, EventBridge kích hoạt Step Functions. Step Functions gọi Lambda để đọc file từ S3, gọi AI bên ngoài, nhận summary/task list và ghi kết quả về DynamoDB.
+ **Monitoring & cost control:** CloudWatch dùng để xem log Lambda/API/pipeline, theo dõi lỗi và hỗ trợ debug khi deploy. AWS Budgets hoặc billing alarm giúp tránh phát sinh chi phí ngoài ý muốn.

Các thành phần như VPC, ALB, EC2 Auto Scaling, NAT Gateway và DynamoDB VPC Endpoint trong sơ đồ kiến trúc là hướng mở rộng cho trường hợp cần dịch vụ Chat & Voice realtime chạy liên tục trong production. Nếu chưa triển khai các thành phần này trong tài khoản AWS, hãy ghi rõ là **thiết kế mở rộng** thay vì phần đã hoàn thành.

![architecture](/images/5-Workshop/5.1-Workshop-overview/architecture.png)

#### Luồng end-to-end của MVP

1. Người dùng mở dashboard qua **CloudFront** và đăng nhập bằng **Cognito**.
2. Dashboard nhận JWT từ Cognito và gọi backend qua **API Gateway**.
3. **Lambda** xử lý nghiệp vụ, đọc/ghi dữ liệu người dùng, cuộc họp và task trong **DynamoDB**.
4. Người dùng upload transcript/audio hoặc hệ thống tạo bundle cuộc họp trong **S3**.
5. Sự kiện upload trong S3 kích hoạt **EventBridge → Step Functions**.
6. Step Functions gọi **Lambda AI processing** để đọc dữ liệu, gọi AI bên ngoài và nhận summary + extracted tasks.
7. Lambda ghi task vào **DynamoDB**, dashboard hiển thị kết quả cho người dùng theo vai trò.
8. **CloudWatch** ghi lại log để kiểm tra quá trình chạy, debug lỗi và làm bằng chứng triển khai.

{{% notice info %}}
Region chính nên dùng cho workshop là **Asia Pacific (Singapore) — ap-southeast-1**. Riêng CloudFront là dịch vụ global, còn WAF gắn với CloudFront phải tạo ở scope **Global (CloudFront)**.
{{% /notice %}}

#### Ảnh minh chứng nên chụp

| Phần cần chứng minh | Ảnh nên chụp trên AWS | Mục đích |
|---|---|---|
| Kiến trúc tổng thể | Sơ đồ architecture của dự án | Cho thấy toàn bộ luồng hệ thống |
| Đăng nhập | Cognito User Pool và App client | Chứng minh có lớp authentication |
| Database | Danh sách DynamoDB tables và một vài item mẫu | Chứng minh dữ liệu users/meetings/tasks được lưu |
| Storage | S3 bucket chứa transcript/audio hoặc meeting bundle | Chứng minh có nơi lưu dữ liệu cuộc họp |
| Backend API | API Gateway routes và Lambda integration | Chứng minh dashboard gọi backend qua API |
| Xử lý AI | Step Functions state machine và execution thành công | Chứng minh pipeline AI chạy được |
| Kết quả AI | Item task được ghi vào DynamoDB | Chứng minh AI output đã thành dữ liệu nghiệp vụ |
| Frontend | CloudFront distribution status `Deployed` và URL dashboard | Chứng minh frontend đã được phân phối |
| Monitoring | CloudWatch log group/log stream của Lambda hoặc Step Functions | Chứng minh có log để debug và vận hành |
| Kiểm thử cuối | Màn hình dashboard sau đăng nhập và danh sách task | Chứng minh luồng end-to-end hoạt động |
