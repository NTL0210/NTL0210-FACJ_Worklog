---
title : "Kiểm thử end-to-end"
date : 2026-01-01 
weight : 8 
chapter : false
pre : " <b> 5.8. </b> "
---

#### Kiểm thử toàn bộ nền tảng

Đi hết hành trình người dùng của MVP và xác nhận từng giai đoạn bằng ảnh minh chứng.

1. **Đăng ký / đăng nhập:** mở URL CloudFront hoặc URL deploy hiện tại của dashboard, đăng ký/đăng nhập bằng **Cognito**, sau đó chụp màn hình dashboard sau khi đăng nhập.


2. **API nghiệp vụ:** thực hiện một thao tác trên dashboard, ví dụ tạo meeting hoặc xem danh sách task. Kiểm tra request đi qua **API Gateway → Lambda** và dữ liệu được đọc/ghi trong **DynamoDB**.


3. **Lưu dữ liệu cuộc họp:** upload transcript/audio hoặc tạo một meeting bundle trong **S3**. Chụp bucket và object theo đường dẫn của meeting.



4. **Pipeline AI:** xác nhận upload S3 kích hoạt **EventBridge → Step Functions**. Chụp state machine và một execution thành công.
5. **Kết quả xử lý:** kiểm tra summary/task list đã được ghi vào **DynamoDB**. Chụp item task hoặc bảng task sau khi xử lý.




6. **Monitoring:** mở **CloudWatch Logs** của Lambda/API/pipeline để chứng minh có log chạy thực tế và có thể debug khi lỗi.


7. **Phần mở rộng (tùy chọn):** nếu đã triển khai VPC/ALB/EC2 realtime, chụp target group healthy, ASG instance và bản ghi chat được lưu qua DynamoDB endpoint. Nếu chưa triển khai, ghi rõ đây là hướng mở rộng.
8. **Thông báo (tùy chọn):** nếu có SNS hoặc notification trong dashboard, chụp thông báo và dữ liệu trạng thái tương ứng trong DynamoDB.

{{% notice tip %}}
Nếu pipeline không khởi động, kiểm tra theo thứ tự: file đã thật sự vào S3 chưa, EventBridge rule có match đúng bucket/object event không, Step Functions role có quyền gọi Lambda không, và CloudWatch Logs của Lambda có lỗi gì không.
{{% /notice %}}
