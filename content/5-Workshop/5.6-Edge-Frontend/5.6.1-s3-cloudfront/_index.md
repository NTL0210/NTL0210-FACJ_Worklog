---
title : "S3 & CloudFront"
date : 2026-01-01 
weight : 1 
chapter : false
pre : " <b> 5.6.1. </b> "
---

#### Host the Next.js dashboard

1. **Build the dashboard:** produce the static export of your Next.js app (`npm run build` and export). Set the API base URL to your API Gateway endpoint and the Cognito settings from section 5.3.
2. **Create the S3 bucket** `aws-meting-frontend` (block all public access — CloudFront will access it privately).
3. Upload the built assets to the bucket.

![frontend bucket](/images/5-Workshop/5.6-Edge-Frontend/5.6.1-s3-cloudfront/frontend-bucket.png)

4. **Create a CloudFront distribution:**
   + Origin = the `aws-meting-frontend` bucket, using **Origin Access Control (OAC)** so only CloudFront can read it.
   + Default root object `index.html`.
   + Redirect HTTP to HTTPS.
5. Update the S3 bucket policy to allow access from the CloudFront distribution (OAC).
6. Note the CloudFront domain name and update the **Cognito app client** callback/sign-out URLs (section 5.3.1) to this domain.

![cloudfront](/images/5-Workshop/5.6-Edge-Frontend/5.6.1-s3-cloudfront/cloudfront.png)

{{% notice tip %}}
Keep the S3 bucket private and serve only through CloudFront + OAC. This is both more secure and cheaper (CloudFront caching reduces origin requests).
{{% /notice %}}
