---
title : "ALB, Auto Scaling & EC2"
date : 2026-01-01 
weight : 2 
chapter : false
pre : " <b> 5.4.2. </b> "
---

#### Deploy the Chat & Voice tier

**1. Security groups**
+ `alb-sg`: allow inbound HTTP/HTTPS from the Internet (the ALB is public-facing for the Chat & Voice endpoint).
+ `ec2-sg`: allow inbound only from `alb-sg` on the app port (for example 3000/8080); no direct Internet ingress.

**2. Launch template**
+ Create a launch template for the Chat & Voice app: an Amazon Linux 2023 AMI, instance type e.g. `t3.medium`, the `ec2-sg` security group, and an **instance profile** allowing `dynamodb:PutItem`/`Query` on `ai-meeting-save-chats`.
+ In **User data**, install and start your Chat & Voice server (WebSocket/voice service). Enable the SSM agent so you can connect without SSH.


**3. Auto Scaling group**
+ Create an ASG using the launch template, spanning the **two private subnets** (AZ A and AZ B).
+ Desired capacity 2, minimum 2, maximum 4. This gives you the Primary/Standby behaviour across AZs and self-healing.
+ Add a target-tracking scaling policy (e.g. average CPU 60%).


When the ASG is scaled up, EC2 instances are launched from the template and registered into the target group.


**4. Application Load Balancer**
+ Create an **internet-facing ALB** in the **two public subnets**, with `alb-sg`.
+ Create a **target group** pointing to the app port with a health check path (e.g. `/health`).
+ Attach the target group to the ASG so new instances register automatically.
+ Add a listener (HTTP/HTTPS) forwarding to the target group. This is the entry for step 18 (Internet access) → step 19 (route to EC2).


Check the target group to verify the health check path and target registration.


{{% notice tip %}}
Test failover: terminate one instance and watch the ASG launch a replacement in a healthy AZ while the ALB keeps serving from the other AZ.
{{% /notice %}}
