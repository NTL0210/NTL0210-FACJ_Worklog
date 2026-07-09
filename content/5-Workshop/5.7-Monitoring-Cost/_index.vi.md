---
title : "Giám sát & Chi phí"
date : 2026-01-01 
weight : 7 
chapter : false
pre : " <b> 5.7. </b> "
---

#### Giám sát và tối ưu chi phí

**Giám sát tập trung cho MVP**
1. Xác nhận **Lambda**, **Step Functions**, **API Gateway** và pipeline xử lý AI đều có logs trong **CloudWatch Logs**.
2. Kiểm tra các chỉ số chính: số lần gọi API, lỗi Lambda, thời lượng Lambda, trạng thái execution của Step Functions và lỗi ghi/đọc DynamoDB.
3. Tạo **CloudWatch dashboard** đơn giản để theo dõi backend API và pipeline AI.
4. Tạo **alarm** cho lỗi quan trọng, ví dụ Lambda `Errors`, Step Functions `ExecutionsFailed` hoặc API Gateway 5xx.

**Giám sát phần mở rộng production**
Nếu triển khai thêm VPC/ALB/EC2 realtime, bổ sung các chỉ số như ALB 5xx, target group unhealthy host, CPU EC2, số instance in-service của ASG và bytes qua NAT Gateway. Các chỉ số này không bắt buộc cho MVP nếu chưa triển khai tầng realtime.

**Vòng đời lưu trữ S3**
1. Trên bucket lưu meeting bundle, ví dụ `ai-meeting-bundle`, thêm **S3 Lifecycle rule** để chuyển transcript/audio cũ sang lớp lưu trữ rẻ hơn sau một khoảng thời gian, ví dụ 30 ngày.
2. Với dữ liệu chỉ dùng để audit hoặc tham khảo lâu dài, có thể expire hoặc chuyển sang Glacier/Deep Archive sau 90-120 ngày tùy nhu cầu.

**Kiểm soát chi phí**
+ Ưu tiên model AI nhẹ hơn hoặc giới hạn token output khi chất lượng vẫn đạt yêu cầu.
+ DynamoDB on-demand và Lambda phù hợp cho MVP vì trả theo lượt dùng.
+ CloudWatch Logs nên đặt retention phù hợp, ví dụ 7-30 ngày trong giai đoạn demo.
+ Với phần production extension, EC2, ALB, NAT Gateway và VPC Endpoint tính phí theo giờ, nên chỉ tạo khi cần test thật và nhớ dọn dẹp sau khi chụp ảnh.

![lambda logs](/images/5-Workshop/5.7-Monitoring-Cost/lambda-logs.png)

{{% notice tip %}}
Ảnh minh chứng nên chụp ở CloudWatch Logs hoặc CloudWatch Dashboard. Nếu chưa tạo dashboard, chỉ cần chụp log group/log stream của Lambda hoặc Step Functions execution là đủ để chứng minh hệ thống có giám sát và debug được.
{{% /notice %}}
