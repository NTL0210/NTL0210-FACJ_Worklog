---
title : "S3 & CloudFront"
date : 2026-01-01 
weight : 1 
chapter : false
pre : " <b> 5.6.1. </b> "
---

#### Host dashboard Next.js

1. **Build dashboard:** tạo bản static export của app Next.js (`npm run build` và export). Đặt API base URL trỏ tới endpoint API Gateway và cấu hình Cognito từ phần 5.3.
2. **Tạo S3 bucket** `aws-meting-frontend` (chặn toàn bộ public access — CloudFront sẽ truy cập riêng tư).
3. Upload các file đã build lên bucket.

![frontend bucket](/images/5-Workshop/5.6-Edge-Frontend/5.6.1-s3-cloudfront/frontend-bucket.png)

4. **Tạo CloudFront distribution:**
   + Origin = bucket `aws-meting-frontend`, dùng **Origin Access Control (OAC)** để chỉ CloudFront đọc được.
   + Default root object `index.html`.
   + Chuyển hướng HTTP sang HTTPS.
5. Cập nhật bucket policy của S3 để cho phép truy cập từ CloudFront distribution (OAC).
6. Ghi lại domain của CloudFront và cập nhật callback/sign-out URL của **Cognito app client** (phần 5.3.1) thành domain này.

![cloudfront](/images/5-Workshop/5.6-Edge-Frontend/5.6.1-s3-cloudfront/cloudfront.png)

{{% notice tip %}}
Giữ S3 bucket ở chế độ riêng tư và chỉ phục vụ qua CloudFront + OAC. Cách này vừa an toàn hơn vừa rẻ hơn (cache của CloudFront giảm request về origin).
{{% /notice %}}
