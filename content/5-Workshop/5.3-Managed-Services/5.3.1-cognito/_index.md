---
title : "Amazon Cognito"
date : 2026-01-01 
weight : 1 
chapter : false
pre : " <b> 5.3.1. </b> "
---

#### Create a Cognito User Pool

Cognito authenticates users and issues the **JWT** that API Gateway will validate (steps 1 and 6).

1. Open the **Cognito console** → **Create user pool**.
2. **Sign-in options:** choose **Email** (and username if you want).
3. Configure password policy and (optionally) enable **MFA**.
4. **Required attributes:** `email`, `name`.
5. Configure the message delivery (use the Cognito default email for the lab).
6. **App client:** create a **public client** for the Next.js dashboard (no client secret for a SPA). Set the callback and sign-out URLs to your CloudFront domain (you can update these later, in section 5.6).
7. Create the pool and note the **User Pool ID** and **App Client ID** — you will use them in the dashboard and in the API Gateway authorizer.

![cognito](/images/5-Workshop/5.3-Managed-Services/5.3.1-cognito/user-pool.png)

After creating the user pool, open **App integration** to verify the app client used by the dashboard.

![cognito app client](/images/5-Workshop/5.3-Managed-Services/5.3.1-cognito/app-client.png)

{{% notice note %}}
For the dashboard, the JWT (ID/Access token) is attached as a `Bearer` token on API requests. API Gateway will verify it against this user pool (section 5.3.3).
{{% /notice %}}
