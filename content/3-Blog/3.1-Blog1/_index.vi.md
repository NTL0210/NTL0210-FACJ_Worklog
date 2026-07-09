---
title: "Xây dựng Ứng dụng Serverless với AWS Lambda và DynamoDB"
date: 2026-07-08
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Xây dựng Ứng dụng Serverless với AWS Lambda và DynamoDB

{{< figure src="/NTL0210-FACJ_Worklog/images/blog1.png" title="Tổng quan Kiến trúc AWS Serverless" >}}

## Giới thiệu

Trong 12 tuần thực tập tại FCJ, tôi đã xây dựng một ứng dụng serverless sẵn sàng production sử dụng AWS Lambda và DynamoDB. Bài viết blog này chia sẻ các quyết định kiến trúc, mẫu thiết kế và best practices mà tôi học được khi triển khai Nền tảng Quản lý Công việc từ Cuộc họp AI đa người thuê.

## Tại sao Serverless?

Kiến trúc Serverless cung cấp nhiều lợi ích hấp dẫn cho ứng dụng hiện đại:

- **Không quản lý server**: Tập trung vào code, không phải hạ tầng
- **Tự động mở rộng**: Xử lý lưu lượng tăng đột biến mà không cần can thiệp thủ công
- **Định giá theo sử dụng**: Chỉ trả tiền cho thời gian tính toán và lưu trữ thực tế
- **High availability**: Dư phòng tích hợp sẵn trên nhiều availability zones
- **Thời gian ra thị trường nhanh hơn**: Triển khai tính năng nhanh chóng mà không cần thiết lập hạ tầng

Đối với dự án thực tập với mô hình sử dụng không thể dự đoán và ngân sách hạn chế, serverless là lựa chọn hoàn hảo.

## Tổng quan Kiến trúc

Nền tảng sử dụng kiến trúc serverless hoàn toàn:

```
Frontend (Next.js trên CloudFront)
        ↓
API Gateway (REST API)
        ↓
AWS Lambda Functions (Express.js)
        ↓
DynamoDB (Thiết kế Single-table)
        ↓
S3 (Bản ghi âm & transcripts)
```

### Thành phần chính:

1. **AWS Lambda**: Chạy logic API backend
2. **Amazon DynamoDB**: Lưu trữ tất cả dữ liệu ứng dụng
3. **Amazon S3**: Lưu trữ bản ghi cuộc họp và transcripts
4. **API Gateway**: Expose REST endpoints
5. **AWS Cognito**: Xử lý xác thực người dùng

## Thiết kế DynamoDB Single-Table

Một trong những quyết định kiến trúc quan trọng nhất là sử dụng **thiết kế single-table** cho DynamoDB. Thay vì tạo các bảng riêng biệt cho từng entity (users, workspaces, meetings, tasks), mọi thứ đều nằm trong một bảng.

### Tại sao Single-Table?

- **Hiệu suất tốt hơn**: Lấy dữ liệu liên quan trong một truy vấn duy nhất
- **Chi phí thấp hơn**: Ít thao tác read/write hơn
- **Mẫu truy cập đơn giản hóa**: Tất cả dữ liệu được tổ chức theo workspace
- **Multi-tenancy dễ dàng hơn**: Cô lập tự nhiên giữa các workspaces

### Cấu trúc Bảng:

```
PK                    SK                      Attributes
---------------------------------------------------------
WORKSPACE#123         METADATA#               name, createdAt, ...
WORKSPACE#123         USER#alice              role, joinedAt, ...
WORKSPACE#123         USER#bob                role, joinedAt, ...
WORKSPACE#123         MEETING#abc             title, date, ...
WORKSPACE#123         TASK#xyz                title, assignee, ...
```

### Global Secondary Indexes (GSI):

**GSI1**: Cho truy vấn dựa trên thời gian
- PK: `WORKSPACE#123`
- SK: `TIMESTAMP#2026-07-08`
- Use case: "Lấy tất cả cuộc họp trong workspace theo ngày"

**GSI2**: Cho truy vấn cụ thể người dùng
- PK: `WORKSPACE#123`
- SK: `ASSIGNEE#alice`
- Use case: "Lấy tất cả nhiệm vụ được giao cho người dùng"

## Thiết kế Lambda Function

Mỗi Lambda function theo một mẫu nhất quán:

### Cấu trúc Function:
```javascript
// handler.js
exports.handler = async (event) => {
  try {
    // 1. Trích xuất và xác thực input
    const { workspaceId, userId } = event;
    
    // 2. Thực hiện business logic
    const result = await processRequest(workspaceId, userId);
    
    // 3. Trả về response
    return {
      statusCode: 200,
      body: JSON.stringify(result)
    };
  } catch (error) {
    // 4. Xử lý lỗi một cách graceful
    console.error(error);
    return {
      statusCode: 500,
      body: JSON.stringify({ error: error.message })
    };
  }
};
```

### Best Practices Áp dụng:

1. **Giữ functions nhỏ**: Mỗi function làm một việc tốt
2. **Sử dụng environment variables**: Lưu cấu hình riêng biệt
3. **Tối ưu hóa cold starts**: Giảm thiểu dependencies và initialization code
4. **Triển khai xử lý lỗi phù hợp**: Log lỗi và trả về thông báo có ý nghĩa
5. **Đặt timeouts phù hợp**: Cân bằng giữa reliability và chi phí

## Triển khai Multi-Tenancy

Hỗ trợ nhiều workspaces (tenants) yêu cầu thiết kế cẩn thận:

### Cô lập Workspace:
- Mọi truy vấn DynamoDB bao gồm `workspaceId` trong partition key
- S3 objects được lưu với prefix workspace: `workspace-123/meeting-abc/audio.mp3`
- Lambda functions xác thực người dùng có quyền truy cập workspace được yêu cầu
- Cognito user pools với custom attributes cho workspace membership

### Kiểm soát Truy cập:
```javascript
async function validateAccess(userId, workspaceId) {
  const membership = await dynamodb.get({
    TableName: 'ai-meeting-workforce',
    Key: {
      PK: `WORKSPACE#${workspaceId}`,
      SK: `USER#${userId}`
    }
  });
  
  if (!membership) {
    throw new Error('Access denied');
  }
  
  return membership.role; // 'admin', 'member', 'guest'
}
```

## Tối ưu hóa Chi phí

Giữ chi phí thấp là rất quan trọng cho dự án thực tập:

### Chiến lược Đã triển khai:

1. **DynamoDB On-Demand Mode**: Chỉ trả cho requests thực tế
2. **Tối ưu hóa Lambda Memory**: Tìm sweet spot ở 512 MB
3. **S3 Lifecycle Policies**: Tự động chuyển sang tiers lưu trữ rẻ hơn
4. **CloudFront Caching**: Giảm Lambda invocations cho nội dung tĩnh
5. **Connection Pooling**: Tái sử dụng DynamoDB connections qua Lambda invocations

### Chi phí Hàng tháng Thực tế:
- Lambda: $2-5
- DynamoDB: $5-12
- S3: $3-8
- **Tổng: ~$44/tháng** cho môi trường phát triển

## Thách thức & Giải pháp

### Thách thức 1: Cold Starts
**Vấn đề**: Request đầu tiên sau thời gian idle mất 2-3 giây  
**Giải pháp**: 
- Giảm kích thước package Lambda
- Sử dụng AWS SDK v3 (nhỏ hơn v2)
- Triển khai connection pooling
- Kết quả: Cold starts giảm xuống < 1 giây

### Thách thức 2: DynamoDB Hot Partitions
**Vấn đề**: Lưu lượng cao đến workspaces cụ thể gây throttling  
**Giải pháp**:
- Chuyển từ provisioned sang on-demand capacity
- Triển khai caching layer cho frequent reads
- Thêm GSIs để phân phối query load

### Thách thức 3: Cô lập Dữ liệu Multi-Tenant
**Vấn đề**: Rủi ro rò rỉ dữ liệu giữa các workspaces  
**Giải pháp**:
- Xác thực nghiêm ngặt trong mỗi Lambda function
- Workspace ID trong mọi DynamoDB key
- Automated tests cho access control
- Code reviews tập trung vào bảo mật

## Bài học Chính

1. **Bắt đầu với single-table design**: Dễ scale hơn nhiều bảng
2. **Giám sát mọi thứ**: CloudWatch metrics là bạn của bạn
3. **Tối ưu hóa chi phí sớm**: Những thay đổi nhỏ tích lũy lại
4. **Kiểm tra multi-tenancy kỹ lưỡng**: Bảo mật là quan trọng
5. **Sử dụng AWS best practices**: Chúng tồn tại vì những lý do tốt

## Kết luận

Xây dựng ứng dụng serverless với AWS Lambda và DynamoDB đã dạy tôi những bài học quý giá về kiến trúc cloud, tối ưu hóa chi phí và phát triển sẵn sàng production. Nền tảng xử lý giao tiếp thời gian thực, xử lý AI và cô lập dữ liệu đa người thuê—tất cả trong khi vẫn ở trong ngân sách thân thiện với sinh viên.

Đối với bất kỳ ai bắt đầu với serverless trên AWS, lời khuyên của tôi là: bắt đầu nhỏ, giám sát chặt chẽ và lặp lại dựa trên các mẫu sử dụng thực tế. Mô hình serverless loại bỏ độ phức tạp của hạ tầng, cho phép bạn tập trung vào giải quyết các vấn đề thực tế.

## Tài nguyên

- [AWS Lambda Best Practices](https://docs.aws.amazon.com/lambda/latest/dg/best-practices.html)
- [DynamoDB Single-Table Design](https://aws.amazon.com/blogs/compute/creating-a-single-table-design-with-amazon-dynamodb/)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [Project GitHub Repository](https://github.com/NTL0210/FACJ_Worklog)

---

**Tác giả:** Nguyễn Tấn Lộc  
**Ngày:** 08/07/2026  
**Tags:** AWS, Lambda, DynamoDB, Serverless, Multi-tenancy, Cloud Architecture
