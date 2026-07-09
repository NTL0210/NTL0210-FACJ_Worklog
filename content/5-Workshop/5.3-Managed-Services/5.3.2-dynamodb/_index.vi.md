---
title : "Amazon DynamoDB"
date : 2026-01-01 
weight : 2 
chapter : false
pre : " <b> 5.3.2. </b> "
---

#### Tạo bảng DynamoDB

Dự án sử dụng một bảng DynamoDB chính là `ai-meeting-platform` theo hướng **single-table design**. Thay vì tách riêng nhiều bảng như relational database, các loại dữ liệu như user, meeting, task, notification và trạng thái xử lý được phân loại bằng key và thuộc tính loại dữ liệu.

Cách thiết kế này phù hợp với DynamoDB vì DynamoDB thường được tối ưu theo **access pattern** của ứng dụng. Với phạm vi MVP, một bảng chính giúp giảm số lượng tài nguyên cần deploy, dễ quản lý quyền IAM hơn và vẫn đáp ứng các truy vấn chính của hệ thống.

1. Mở **DynamoDB console** → **Create table**.
2. Tạo bảng `ai-meeting-platform`.
3. Chọn capacity mode **On-demand** để phù hợp với giai đoạn demo/MVP.
4. Giữ mã hóa khi lưu ở cấu hình mặc định.

![dynamodb table](/images/5-Workshop/5.3-Managed-Services/5.3.2-dynamodb/tables.png)

Sau khi backend hoặc pipeline AI chạy, mở **Explore table items** để kiểm tra dữ liệu meeting/task đã được ghi vào bảng.

![dynamodb task item](/images/5-Workshop/5.3-Managed-Services/5.3.2-dynamodb/task-item.png)

{{% notice note %}}
Single-table design là cách thiết kế phổ biến trong DynamoDB. Khi hệ thống lớn hơn, có thể bổ sung GSI hoặc tách thêm bảng nếu access pattern thay đổi.
{{% /notice %}}
