---
title: "Xây dựng Ứng dụng Serverless với AWS Lambda và DynamoDB"
date: 2025-01-15
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Xây dựng Ứng dụng Serverless với AWS Lambda và DynamoDB

{{< figure src="/NTL0210-FACJ_Worklog/images/blog1.png" title="Tổng quan Kiến trúc AWS Serverless" >}}

Trong 12 tuần thực tập tại FCJ, tôi đã xây dựng một ứng dụng serverless sẵn sàng cho production sử dụng AWS Lambda và DynamoDB. Bài viết này chia sẻ các quyết định kiến trúc, mẫu thiết kế và best practices mà tôi đã học được khi triển khai nền tảng AI Meeting Workforce Platform đa người thuê.

---

## Tại sao chọn Serverless?

Kiến trúc serverless cung cấp nhiều lợi ích hấp dẫn:
- **Không cần quản lý server**: Tập trung vào code, không phải infrastructure
- **Tự động mở rộng quy mô**: Xử lý lưu lượng tăng đột biến mà không cần can thiệp thủ công
- **Giá theo mức sử dụng**: Chỉ trả tiền cho thời gian tính toán thực tế
- **Độ khả dụng cao**: Tính dự phòng tích hợp sẵn trên nhiều availability zones

Đối với dự án thực tập với mẫu sử dụng khó dự đoán và ngân sách hạn chế, serverless là lựa chọn hoàn hảo.

---

## Thiết kế Single-Table cho DynamoDB

Một trong những quyết định kiến trúc quan trọng nhất là sử dụng **thiết kế single-table** cho DynamoDB. Thay vì tạo các bảng riêng biệt cho mỗi entity, mọi thứ đều nằm trong một bảng với cấu trúc này:

```
PK                    SK                      Attributes
---------------------------------------------------------
WORKSPACE#123         METADATA#               name, createdAt, ...
WORKSPACE#123         USER#alice              role, joinedAt, ...
WORKSPACE#123         MEETING#abc             title, date, ...
WORKSPACE#123         TASK#xyz                title, assignee, ...
```

### Lợi ích:
- **Hiệu suất tốt hơn**: Truy xuất dữ liệu liên quan trong một query duy nhất
- **Chi phí thấp hơn**: Ít thao tác đọc/ghi hơn
- **Đơn giản hóa access patterns**: Tất cả dữ liệu được tổ chức theo workspace
- **Dễ dàng multi-tenancy**: Cách ly tự nhiên giữa các workspaces

---

## Thách thức Chính & Giải pháp

### Thách thức 1: Cold Starts
- **Vấn đề**: Request đầu tiên sau thời gian idle mất 2-3 giây
- **Giải pháp**: Giảm kích thước package Lambda, sử dụng AWS SDK v3, triển khai connection pooling
- **Kết quả**: Cold starts giảm xuống < 1 giây

### Thách thức 2: Cách ly Dữ liệu Multi-Tenant
- **Vấn đề**: Nguy cơ rò rỉ dữ liệu giữa các workspaces
- **Giải pháp**: Xác thực nghiêm ngặt trong mọi Lambda function, workspace ID trong mọi key DynamoDB
- **Kết quả**: Không có sự cố rò rỉ dữ liệu trên tất cả các workspaces

### Thách thức 3: Quản lý Chi phí
- **Vấn đề**: Giá on-demand của DynamoDB khó dự đoán
- **Giải pháp**: Triển khai lớp caching, tối ưu hóa queries sử dụng GSIs
- **Kết quả**: Giữ chi phí hàng tháng dưới $15

---

## Tối ưu hóa Hiệu suất

### Tối ưu hóa Lambda:
```javascript
// Trước: 2.5s cold start
import AWS from 'aws-sdk';

// Sau: <1s cold start
import { DynamoDBClient } from '@aws-sdk/client-dynamodb';
import { DynamoDBDocumentClient } from '@aws-sdk/lib-dynamodb';
```

### Tái sử dụng Connection:
```javascript
// Global scope - được tái sử dụng qua các invocations
const dynamoClient = DynamoDBDocumentClient.from(
  new DynamoDBClient({ region: 'ap-southeast-1' })
);

export const handler = async (event) => {
  // Sử dụng connection hiện có
  const result = await dynamoClient.send(new GetCommand({...}));
};
```

---

## Bài học Rút ra

1. **Single-table design** rất mạnh mẽ nhưng cần lập kế hoạch cẩn thận
2. **AWS SDK v3** giảm đáng kể thời gian cold start
3. **Connection pooling** là điều cần thiết cho các Lambda function production
4. **Multi-tenancy** yêu cầu xác thực nghiêm ngặt ở mọi lớp
5. **Serverless** hoàn hảo cho workloads khó dự đoán với ngân sách hạn chế

Xây dựng nền tảng serverless này đã dạy tôi rằng AWS Lambda và DynamoDB không chỉ là buzzwords—chúng là các công cụ sẵn sàng cho production có thể xử lý ứng dụng thực tế khi được thiết kế đúng cách.

---

## Bài viết Liên quan

- [Giao tiếp Voice Thời gian thực với WebRTC trên AWS](../3.2-blog2/)
- [Gỡ lỗi Vấn đề Production trên CloudFront CDN](../3.3-blog3/)

---

*Được viết trong 12 tuần thực tập tại FCJ (17/4 - 10/7/2026) như một phần của dự án AI Meeting Workforce Platform.*
