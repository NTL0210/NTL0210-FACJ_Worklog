---
title : "Managed Services"
date : 2026-01-01 
weight : 3 
chapter : false
pre : " <b> 5.3. </b> "
---

#### The managed / serverless layer

You will first build the managed services that run **outside the VPC** — the identity, data, and business API that the dashboard talks to. This is the core of the MVP: users sign in, the dashboard sends JWTs to the API, Lambda handles business logic, and DynamoDB stores the project data.

1. [Amazon Cognito — authentication & JWT](5.3.1-cognito/)
2. [Amazon DynamoDB — data tables](5.3.2-dynamodb/)
3. [API Lambda & API Gateway](5.3.3-api-lambda-gateway/)
