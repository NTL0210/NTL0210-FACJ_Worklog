---
title : "AWS WAF"
date : 2026-01-01 
weight : 2 
chapter : false
pre : " <b> 5.6.2. </b> "
---

#### Protect the edge with WAF

1. Open the **WAF console** (for CloudFront, use the **Global (CloudFront)** scope, which lives in us-east-1).
2. **Create a Web ACL** or use the Web ACL created by CloudFront, for example `CreatedByCloudFront-f75e6549`.
3. Add **AWS Managed Rules**, for example:
   + **Core rule set (CRS)** — common web exploits.
   + **Known bad inputs**.
   + **Amazon IP reputation list** and/or a **rate-based rule** to blunt bots and floods.
4. Set the default action to **Allow** (managed rules block malicious requests).
5. **Associate** the Web ACL with your CloudFront distribution (step 3: filter request).

![waf](/images/5-Workshop/5.6-Edge-Frontend/5.6.2-waf/waf.png)

{{% notice note %}}
A CloudFront-scoped Web ACL must be created in **us-east-1**, even though the rest of the workshop is in ap-southeast-1. This is expected for global (edge) resources.
{{% /notice %}}
