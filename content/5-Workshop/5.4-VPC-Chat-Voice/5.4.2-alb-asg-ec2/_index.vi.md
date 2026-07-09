---
title : "ALB, Auto Scaling & EC2"
date : 2026-01-01 
weight : 2 
chapter : false
pre : " <b> 5.4.2. </b> "
---

#### Triển khai tầng Chat & Voice

**1. Security group**
+ `alb-sg`: cho phép HTTP/HTTPS đi vào từ Internet (ALB hướng ra ngoài cho endpoint Chat & Voice).
+ `ec2-sg`: chỉ cho phép vào từ `alb-sg` trên cổng ứng dụng (ví dụ 3000/8080); không mở Internet trực tiếp.

**2. Launch template**
+ Tạo launch template cho app Chat & Voice: AMI Amazon Linux 2023, loại instance ví dụ `t3.medium`, security group `ec2-sg`, và **instance profile** cho phép `dynamodb:PutItem`/`Query` trên `ai-meeting-save-chats`.
+ Trong **User data**, cài và khởi động server Chat & Voice (dịch vụ WebSocket/voice). Bật SSM agent để kết nối không cần SSH.


**3. Auto Scaling group**
+ Tạo ASG dùng launch template, trải trên **hai private subnet** (AZ A và AZ B).
+ Desired 2, min 2, max 4. Điều này tạo hành vi Primary/Standby giữa các AZ và tự phục hồi.
+ Thêm chính sách scale target-tracking (ví dụ CPU trung bình 60%).


Khi ASG được scale lên, các EC2 instance sẽ được tạo từ launch template và đăng ký vào target group.


**4. Application Load Balancer**
+ Tạo **ALB internet-facing** trong **hai public subnet**, gắn `alb-sg`.
+ Tạo **target group** trỏ tới cổng ứng dụng với health check path (ví dụ `/health`).
+ Gắn target group vào ASG để instance mới tự đăng ký.
+ Thêm listener (HTTP/HTTPS) chuyển tới target group. Đây là điểm vào của bước 18 (Internet access) → bước 19 (route tới EC2).


Kiểm tra target group để xác nhận health check path và target registration.


{{% notice tip %}}
Kiểm tra failover: tắt một instance và quan sát ASG tạo instance thay thế ở AZ khỏe, trong khi ALB vẫn phục vụ từ AZ còn lại.
{{% /notice %}}
