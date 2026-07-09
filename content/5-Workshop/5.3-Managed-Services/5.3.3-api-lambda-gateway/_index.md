---
title : "API Lambda & API Gateway"
date : 2026-01-01 
weight : 3 
chapter : false
pre : " <b> 5.3.3. </b> "
---

#### Build the business API

This implements steps 5–8: the dashboard calls **API Gateway**, which validates the **Cognito JWT** and invokes the **API Lambda**, which reads/writes **DynamoDB (API Data)**.

**1. Create the API Lambda**
+ Runtime **Node.js 18+**. Function name `ai-meeting-api`.
+ Grant an execution role with least-privilege access to the `ai-meeting-api-data` table.
+ Implement your business routes (e.g. `GET /me`, `POST /sessions`, `GET /tasks`, `POST /meetings`).

![lambda api](/images/5-Workshop/5.3-Managed-Services/5.3.3-api-lambda-gateway/lambda-api.png)

In **Configuration → Environment variables**, configure the audio bucket, DynamoDB table, Cognito settings, and AI model. Secret values such as API keys should be hidden in the report.

![lambda env](/images/5-Workshop/5.3-Managed-Services/5.3.3-api-lambda-gateway/lambda-env.png)

**2. Create the HTTP API in API Gateway**
+ Open **API Gateway** → **Create API** → **HTTP API**.
+ Add an integration to the `ai-meeting-api` Lambda.
+ Define routes and attach the Lambda integration.

**3. Add a JWT authorizer (Cognito)**
+ Create a **JWT authorizer** using your Cognito **issuer URL** (`https://cognito-idp.ap-southeast-1.amazonaws.com/<userPoolId>`) and the **App Client ID** as the audience.
+ Attach the authorizer to the protected routes so requests without a valid token are rejected.

**4. Test**
+ Sign in a test user in Cognito, obtain a token, and call a protected route with `Authorization: Bearer <token>`.

![api gateway](/images/5-Workshop/5.3-Managed-Services/5.3.3-api-lambda-gateway/http-api.png)

After deploying the stage, note the invoke URL used by the frontend.

![api stage url](/images/5-Workshop/5.3-Managed-Services/5.3.3-api-lambda-gateway/api-stage-url.png)
