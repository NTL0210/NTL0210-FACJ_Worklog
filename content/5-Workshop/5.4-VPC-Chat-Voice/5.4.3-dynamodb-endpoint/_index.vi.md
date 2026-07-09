---
title : "DynamoDB VPC Endpoint"
date : 2026-01-01 
weight : 3 
chapter : false
pre : " <b> 5.4.3. </b> "
---

#### Truy cập DynamoDB riêng tư qua VPC endpoint

Để tài nguyên trong private subnet truy cập DynamoDB mà không cần đi qua Internet công cộng, tạo **Gateway VPC Endpoint** cho DynamoDB và gắn vào route table của private subnet. Trong setup hiện tại, VPC cũng có thêm Gateway Endpoint cho S3 để hỗ trợ truy cập bucket từ private subnet.

1. Mở **VPC console** → **Endpoints** → **Create endpoint**.
2. Service: chọn `com.amazonaws.ap-southeast-1.dynamodb`.
3. Endpoint type: **Gateway**.
4. VPC: `app-vpc`.
5. Route tables: chọn route table private, ví dụ `rt-private-a` và `rt-private-b`.
6. Tạo endpoint và kiểm tra trạng thái **Available**.

![dynamodb endpoint](/images/5-Workshop/5.4-VPC-Chat-Voice/5.4.3-dynamodb-endpoint/endpoint.png)

{{% notice note %}}
DynamoDB Gateway Endpoint khác với Interface Endpoint: Gateway Endpoint được gắn vào route table, không tạo ENI trong subnet. Đây là cách phù hợp và tiết kiệm hơn cho DynamoDB/S3 trong VPC.
{{% /notice %}}
