---
title : "AWS WAF"
date : 2026-01-01 
weight : 2 
chapter : false
pre : " <b> 5.6.2. </b> "
---

#### Bảo vệ biên bằng WAF

1. Mở **WAF console** (cho CloudFront, dùng scope **Global (CloudFront)**, nằm ở us-east-1).
2. **Tạo Web ACL** hoặc dùng Web ACL được CloudFront tạo tự động, ví dụ `CreatedByCloudFront-f75e6549`.
3. Thêm **AWS Managed Rules**, ví dụ:
   + **Core rule set (CRS)** — các lỗ hổng web phổ biến.
   + **Known bad inputs**.
   + **Amazon IP reputation list** và/hoặc **rate-based rule** để hạn chế bot và flood.
4. Đặt default action là **Allow** (managed rules sẽ chặn request độc hại).
5. **Associate** Web ACL với CloudFront distribution của bạn (bước 3: filter request).

![waf](/images/5-Workshop/5.6-Edge-Frontend/5.6.2-waf/waf.png)

{{% notice note %}}
Web ACL cho CloudFront phải được tạo ở **us-east-1**, dù phần còn lại của workshop ở ap-southeast-1. Đây là điều bình thường với tài nguyên global (edge).
{{% /notice %}}
