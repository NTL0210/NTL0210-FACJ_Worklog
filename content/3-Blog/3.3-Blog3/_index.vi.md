---
title: "Ước tính Chi phí AWS với AWS Pricing Calculator"
date: 2025-01-15
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Ước tính Chi phí AWS với AWS Pricing Calculator

{{< figure src="/NTL0210-FACJ_Worklog/images/blog3.png" title="AWS Pricing Calculator" >}}

Chào anh em AWS Study Group VN!

Trong quá trình học và thực hành AWS, mình nhận ra một câu hỏi xuất hiện rất thường xuyên:

> **"Triển khai một dịch vụ sẽ tốn bao nhiêu tiền mỗi tháng?"**

Thay vì tạo tài nguyên thật rồi chờ hóa đơn cuối tháng, AWS cung cấp một công cụ miễn phí giúp chúng ta ước tính chi phí trước khi triển khai, đó là **AWS Pricing Calculator**.

Trong bài viết này, mình sẽ chia sẻ cách sử dụng AWS Pricing Calculator để tính chi phí cho một EC2 cơ bản.

---

## 1. AWS PRICING CALCULATOR LÀ GÌ?

**AWS Pricing Calculator** là công cụ cho phép bạn tạo các kịch bản cho khối lượng công việc mới hoặc thay đổi khối lượng công việc hiện có để ước tính chi phí.

### Đặc điểm:

✅ **Miễn phí hoàn toàn**: Không mất phí sử dụng công cụ  
✅ **Không cần đăng nhập AWS**: Có thể sử dụng ngay trên trình duyệt  
✅ **Hỗ trợ nhiều dịch vụ**: EC2, S3, RDS, Lambda, và hàng trăm dịch vụ AWS khác  
✅ **Chia sẻ estimate**: Có thể export hoặc chia sẻ link với team

---

## 2. VÌ SAO NÊN SỬ DỤNG AWS PRICING CALCULATOR?

Theo mình, công cụ này hữu ích trong các trường hợp:

### Lập kế hoạch Chi phí

- Ước tính được chi phí trước khi triển khai hệ thống
- Lập kế hoạch ngân sách cho dự án
- So sánh chi phí giữa các tùy chọn khác nhau

### Chia sẻ với Team

- Chia sẻ estimate với khách hàng hoặc thành viên trong nhóm
- Export thành PDF hoặc CSV cho báo cáo
- Lưu lại các kịch bản để tham khảo sau

### Tối ưu Chi phí

- So sánh các instance types khác nhau
- Đánh giá tác động của Reserved Instances hoặc Savings Plans
- Tính toán chi phí khi scale up/down

---

## 3. QUY TRÌNH SỬ DỤNG AWS PRICING CALCULATOR

Theo bài viết của AWS, quy trình triển khai có thể tóm tắt như sau:

### Bước 1: Truy cập AWS Pricing Calculator

Truy cập: [https://calculator.aws](https://calculator.aws)

Không cần đăng nhập AWS Account.

### Bước 2: Tạo Estimate

Click **"Create estimate"** để bắt đầu.

### Bước 3: Chọn dịch vụ cần tính chi phí

- Tìm kiếm dịch vụ (ví dụ: EC2, S3, RDS)
- Click **"Configure"** trên dịch vụ muốn thêm

### Bước 4: Nhập các thông số

Đối với EC2, các thông số cần nhập:

#### Location (Region)
- Chọn AWS Region (ví dụ: Asia Pacific - Singapore)
- Chi phí khác nhau giữa các regions

#### Instance Type
- Family: General Purpose, Compute Optimized, Memory Optimized...
- Size: t3.micro, t3.small, t3.medium...
- vCPUs và Memory

#### Operating System
- Linux, Windows, RHEL, SUSE...
- Windows thường đắt hơn Linux

#### Workload
- **On-Demand**: Trả theo giờ, linh hoạt
- **Reserved Instances**: Cam kết 1-3 năm, tiết kiệm 30-70%
- **Savings Plans**: Linh hoạt hơn RI, tiết kiệm đến 72%
- **Spot Instances**: Giá thấp nhất nhưng có thể bị gián đoạn

#### Utilization
- Số giờ chạy mỗi ngày
- Số ngày chạy mỗi tháng

#### Storage
- EBS Volume Type: gp3, gp2, io2, st1...
- Storage Amount (GB)
- IOPS và Throughput (nếu cần)

#### Data Transfer
- Data Transfer Out to Internet
- Data Transfer between Regions
- Elastic IP (nếu sử dụng)

### Bước 5: Review Estimate

Sau khi nhập đủ thông số, AWS Pricing Calculator sẽ hiển thị:

- **Monthly cost**: Chi phí hàng tháng ước tính
- **12 months total**: Tổng chi phí 12 tháng
- **Breakdown by service**: Chi tiết theo từng thành phần

### Bước 6: Thêm dịch vụ khác (nếu cần)

Có thể tiếp tục thêm các dịch vụ khác như:
- S3 for storage
- RDS for database
- CloudFront for CDN
- Lambda for serverless functions

### Bước 7: Lưu và Chia sẻ

- **Save estimate**: Tạo link chia sẻ
- **Export to PDF**: Xuất báo cáo PDF
- **Export to CSV**: Xuất dữ liệu Excel

---

## 4. VÍ DỤ THỰC TẾ: TÍNH CHI PHÍ EC2

Giả sử mình muốn triển khai một Web Server với cấu hình:

### Cấu hình:

- **Region**: Asia Pacific (Singapore)
- **Instance Type**: t3.medium (2 vCPUs, 4 GB RAM)
- **OS**: Amazon Linux (free)
- **Workload**: On-Demand
- **Usage**: 24 hours/day, 30 days/month
- **Storage**: 30 GB gp3
- **Data Transfer Out**: 100 GB/month

### Kết quả ước tính:

```
EC2 Instance Cost:          $30.40/month
EBS Storage (30 GB gp3):    $2.40/month
Data Transfer Out (100 GB): $9.00/month
─────────────────────────────────────────
TOTAL:                      $41.80/month
```

### So sánh với Reserved Instance (1 year):

```
EC2 Instance Cost:          $20.00/month (-34%)
EBS Storage (30 GB gp3):    $2.40/month
Data Transfer Out (100 GB): $9.00/month
─────────────────────────────────────────
TOTAL:                      $31.40/month
SAVINGS:                    $10.40/month ($124.80/year)
```

---

## 5. MỘT SỐ LƯU Ý

### AWS Pricing Calculator chỉ cung cấp chi phí ước tính

Chi phí thực tế có thể thay đổi tùy thuộc vào:

⚠️ **Mức sử dụng thực tế**: Nếu sử dụng nhiều hơn dự kiến  
⚠️ **Lưu lượng mạng phát sinh**: Data transfer có thể tăng đột biến  
⚠️ **Các dịch vụ bổ sung**: Backup, Snapshot, Elastic IP...  
⚠️ **Thay đổi về giá dịch vụ theo Region**: AWS có thể điều chỉnh giá

### Không bao gồm Free Tier

AWS Pricing Calculator **KHÔNG tự động áp dụng Free Tier**. Nếu bạn đang sử dụng Free Tier, chi phí thực tế sẽ thấp hơn estimate.

**AWS Free Tier cho EC2:**
- 750 hours/month t2.micro hoặc t3.micro
- 30 GB EBS Storage
- 15 GB Data Transfer Out

### Một số chi phí ẩn cần lưu ý

- **Elastic IP không sử dụng**: $0.005/hour
- **EBS Snapshots**: Tính theo GB-month
- **Data Transfer giữa AZs**: $0.01/GB
- **CloudWatch Logs**: Có thể phát sinh chi phí

---

## 6. TIPS SỬ DỤNG HIỆU QUẢ

### Tip 1: Bắt đầu với On-Demand rồi tối ưu sau

- Dùng On-Demand trong giai đoạn đầu
- Theo dõi usage pattern 1-2 tháng
- Chuyển sang Reserved Instances hoặc Savings Plans sau

### Tip 2: So sánh nhiều scenarios

- Tạo nhiều estimates với cấu hình khác nhau
- So sánh chi phí và hiệu năng
- Chọn phương án phù hợp với ngân sách

### Tip 3: Sử dụng Tags để group services

- Đặt tên rõ ràng cho từng service trong estimate
- Group theo môi trường: Dev, Staging, Production
- Dễ dàng theo dõi và so sánh

### Tip 4: Export và lưu trữ estimates

- Lưu lại các estimates cho từng dự án
- Export PDF để đính kèm proposal
- So sánh với hóa đơn thực tế để điều chỉnh

---

## 7. TỔNG KẾT

Theo mình, **AWS Pricing Calculator** là một công cụ hữu ích đối với bất kỳ ai đang học hoặc làm việc với AWS.

### Lợi ích chính:

✅ Ước tính chi phí trước khi triển khai  
✅ So sánh các lựa chọn khác nhau  
✅ Lập kế hoạch ngân sách chi tiết  
✅ Chia sẻ với team và khách hàng  
✅ Tối ưu hóa chi phí AWS

Thay vì triển khai tài nguyên rồi mới kiểm tra chi phí, chúng ta có thể chủ động ước tính trước ngân sách, so sánh các lựa chọn và đưa ra quyết định phù hợp ngay từ giai đoạn thiết kế hệ thống.

Nếu anh em đã từng sử dụng AWS Pricing Calculator hoặc có kinh nghiệm về tối ưu chi phí trên AWS thì có thể chia sẻ thêm ở phần bình luận nhé.

---

## NGUỒN THAM KHẢO

* [AWS Pricing Calculator](https://calculator.aws)
* [AWS Pricing Calculator Documentation](https://aws.amazon.com/premiumsupport/knowledge-center/estimate-aws-monthly-billing-cost/)
* [AWS Free Tier](https://aws.amazon.com/free/)
* [AWS Cost Management](https://aws.amazon.com/aws-cost-management/)

---

## Bài viết Liên quan

- [KIRO – AI Coding Assistant của AWS](../3.1-blog1/)
- [Chạy ứng dụng Web truyền thống trên AWS Nitro Enclaves](../3.2-blog2/)

---

*Được viết trong thời gian thực tập tại FCJ (17/4 - 10/7/2026) và chia sẻ với AWS Study Group VN.*
