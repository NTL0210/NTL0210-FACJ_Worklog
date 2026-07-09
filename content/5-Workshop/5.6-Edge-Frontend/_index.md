---
title : "Edge & Frontend"
date : 2026-01-01 
weight : 6 
chapter : false
pre : " <b> 5.6. </b> "
---

#### Serve the dashboard securely at the edge

The Next.js dashboard is served as static assets from S3 through CloudFront. For the MVP, the most important evidence is the frontend bucket, the CloudFront distribution, and a working dashboard URL. AWS WAF can be added as a production protection layer.

1. [S3 static hosting & CloudFront](5.6.1-s3-cloudfront/)
2. [AWS WAF](5.6.2-waf/)
