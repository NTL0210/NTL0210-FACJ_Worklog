---
title : "Dịch vụ Managed"
date : 2026-01-01 
weight : 3 
chapter : false
pre : " <b> 5.3. </b> "
---

#### Tầng managed / serverless

Trước tiên, bạn xây dựng các dịch vụ managed chạy **ngoài VPC** — phần định danh, dữ liệu và API nghiệp vụ mà dashboard giao tiếp. Đây là lõi của MVP: người dùng đăng nhập, dashboard gửi JWT tới API, Lambda xử lý nghiệp vụ và DynamoDB lưu dữ liệu chính của dự án.

1. [Amazon Cognito — xác thực & JWT](5.3.1-cognito/)
2. [Amazon DynamoDB — các bảng dữ liệu](5.3.2-dynamodb/)
3. [API Lambda & API Gateway](5.3.3-api-lambda-gateway/)
