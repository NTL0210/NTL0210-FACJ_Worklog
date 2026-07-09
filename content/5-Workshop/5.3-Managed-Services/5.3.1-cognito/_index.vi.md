---
title : "Amazon Cognito"
date : 2026-01-01 
weight : 1 
chapter : false
pre : " <b> 5.3.1. </b> "
---

#### Tạo Cognito User Pool

Cognito xác thực người dùng và phát **JWT** để API Gateway kiểm tra (bước 1 và 6).

1. Mở **Cognito console** → **Create user pool**.
2. **Sign-in options:** chọn **Email** (và username nếu muốn).
3. Cấu hình chính sách mật khẩu và (tùy chọn) bật **MFA**.
4. **Thuộc tính bắt buộc:** `email`, `name`.
5. Cấu hình gửi email (dùng email mặc định của Cognito cho lab).
6. **App client:** tạo một **public client** cho dashboard Next.js (SPA không có client secret). Đặt callback và sign-out URL trỏ về domain CloudFront (có thể cập nhật sau ở phần 5.6).
7. Tạo pool và ghi lại **User Pool ID** và **App Client ID** — dùng cho dashboard và cho authorizer của API Gateway.

![cognito](/images/5-Workshop/5.3-Managed-Services/5.3.1-cognito/user-pool.png)

Sau khi tạo user pool, mở phần **App integration** để kiểm tra app client dùng cho dashboard.

![cognito app client](/images/5-Workshop/5.3-Managed-Services/5.3.1-cognito/app-client.png)

{{% notice note %}}
Ở dashboard, JWT (ID/Access token) được gắn dưới dạng token `Bearer` trên các request API. API Gateway sẽ xác thực token này với user pool (phần 5.3.3).
{{% /notice %}}
