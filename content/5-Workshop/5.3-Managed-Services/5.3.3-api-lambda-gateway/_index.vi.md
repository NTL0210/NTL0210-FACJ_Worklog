---
title : "API Lambda & API Gateway"
date : 2026-01-01 
weight : 3 
chapter : false
pre : " <b> 5.3.3. </b> "
---

#### Xây dựng API nghiệp vụ

Phần này hiện thực bước 5–8: dashboard gọi **API Gateway**, API Gateway kiểm tra **JWT của Cognito** rồi gọi **API Lambda**, Lambda đọc/ghi **DynamoDB (API Data)**.

**1. Tạo API Lambda**
+ Runtime **Node.js 18+**. Tên hàm `ai-meeting-api`.
+ Cấp execution role với quyền tối thiểu truy cập bảng `ai-meeting-api-data`.
+ Hiện thực các route nghiệp vụ (ví dụ `GET /me`, `POST /sessions`, `GET /tasks`, `POST /meetings`).

![lambda api](/images/5-Workshop/5.3-Managed-Services/5.3.3-api-lambda-gateway/lambda-api.png)

Trong phần **Configuration → Environment variables**, cấu hình bucket audio, bảng DynamoDB, Cognito và AI model. Các giá trị secret như API key cần được che khi đưa vào báo cáo.

![lambda env](/images/5-Workshop/5.3-Managed-Services/5.3.3-api-lambda-gateway/lambda-env.png)

**2. Tạo HTTP API trong API Gateway**
+ Mở **API Gateway** → **Create API** → **HTTP API**.
+ Thêm integration tới Lambda `ai-meeting-api`.
+ Định nghĩa các route và gắn integration Lambda.

**3. Thêm JWT authorizer (Cognito)**
+ Tạo **JWT authorizer** dùng **issuer URL** của Cognito (`https://cognito-idp.ap-southeast-1.amazonaws.com/<userPoolId>`) và **App Client ID** làm audience.
+ Gắn authorizer vào các route cần bảo vệ để chặn request không có token hợp lệ.

**4. Kiểm tra**
+ Đăng nhập một user thử trong Cognito, lấy token, và gọi một route được bảo vệ với `Authorization: Bearer <token>`.

![api gateway](/images/5-Workshop/5.3-Managed-Services/5.3.3-api-lambda-gateway/http-api.png)

Sau khi deploy stage, ghi lại invoke URL để frontend gọi API.

![api stage url](/images/5-Workshop/5.3-Managed-Services/5.3.3-api-lambda-gateway/api-stage-url.png)
