---
title : "Edge & Frontend"
date : 2026-01-01 
weight : 6 
chapter : false
pre : " <b> 5.6. </b> "
---

#### Phân phối dashboard an toàn ở biên

Dashboard Next.js được phục vụ dưới dạng tài nguyên tĩnh từ S3 qua CloudFront. Với MVP, ảnh minh chứng quan trọng nhất là bucket frontend, CloudFront distribution và URL dashboard chạy được. AWS WAF có thể thêm như lớp bảo vệ ở production.

1. [S3 static hosting & CloudFront](5.6.1-s3-cloudfront/)
2. [AWS WAF](5.6.2-waf/)
