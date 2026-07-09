---
title: "Debugging Production Issues on CloudFront CDN"
date: 2025-01-15
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Debugging Production Issues on CloudFront CDN

{{< figure src="/NTL0210-FACJ_Worklog/images/blog3.png" title="CloudFront Debugging Process" >}}

Week 11 of my FCJ internship was intense—after deploying my serverless application to CloudFront, multiple production issues emerged. This post documents the debugging journey and lessons learned from production deployment.

---

## The Deployment Scenario

After 10 weeks of local development, it was time to deploy to production:
- **Frontend**: Next.js static site on CloudFront + S3
- **Backend**: Express.js Lambda functions behind API Gateway
- **Real-time**: WebRTC signaling server on separate Lambda
- **Authentication**: AWS Cognito with JWT tokens

Everything worked perfectly in local development (`localhost:3000`). Then I deployed to CloudFront...

---

## The Issues Cascade

**Four Critical Production Errors:**
1. ❌ **Login fails** - Users cannot authenticate
2. ❌ **Voice chat fails** - WebRTC connections don't establish
3. ❌ **Text chat unstable** - Messages drop or delay
4. ❌ **Workspace invitations fail** - Email invites not sending

---

## Issue 1: Login Authentication Failure

### Root Cause:
- Backend was setting cookie with `domain=localhost`
- CloudFront domain mismatch
- CORS headers incomplete

### The Fix:
```javascript
// backend/auth.js
const DOMAIN = process.env.NODE_ENV === 'production' 
  ? '.cloudfront.net'  // Match CloudFront domain
  : 'localhost';

res.cookie('auth_token', token, {
  httpOnly: true,
  secure: true,
  sameSite: 'none', // Required for cross-origin
  domain: DOMAIN,
  maxAge: 7 * 24 * 60 * 60 * 1000 // 7 days
});
```

**Result:** ✅ Login works, cookies persist across requests

---

## Issue 2: Voice Chat WebRTC Failure

### Root Cause:
- API Gateway WebSocket timeout (30 seconds through CloudFront)
- Missing WebSocket upgrade headers
- Incorrect origin for STUN/TURN

### The Fix:
1. Configured CloudFront WebSocket timeout to 3600 seconds
2. Implemented ping/pong heartbeat mechanism
3. Used public STUN servers in production

**Result:** ✅ Voice chat connects reliably, WebSocket stays alive

---

## Issue 3: Text Chat Message Instability

### Root Cause:
- No automatic reconnection logic
- CloudFront closing idle WebSocket connections
- No queuing for failed messages

### The Fix:
Implemented automatic reconnection with exponential backoff and message queuing.

**Result:** ✅ Messages deliver reliably, automatic reconnection works

---

## Issue 4: Workspace Invitation Email Failures

### Root Cause:
Lambda function couldn't send emails through AWS SES due to missing IAM permissions.

### The Fix:
```json
// IAM policy for Lambda execution role
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ses:SendEmail",
        "ses:SendRawEmail"
      ],
      "Resource": "*"
    }
  ]
}
```

**Result:** ✅ Invitation emails delivered successfully

---

## Key Lessons Learned

1. **Local Development ≠ Production**
   - Always test in staging environment that mirrors production
   - CloudFront has different behaviors than localhost

2. **Cookie Configuration Matters**
   - `domain`, `sameSite`, `secure` settings are critical
   - Production needs `sameSite: 'none'` for cross-origin cookies

3. **WebSocket Through CDN is Tricky**
   - CloudFront has 30-second idle timeout by default
   - Implement ping/pong heartbeat to keep connections alive

4. **Always Implement Reconnection Logic**
   - Networks are unreliable, plan for disconnections
   - Exponential backoff prevents server overload

5. **IAM Permissions Are Not Optional**
   - Every AWS service needs explicit permissions
   - Monitor CloudWatch logs for permission errors

6. **Monitor Everything in Production**
   - CloudWatch logs saved debugging time
   - Real-time monitoring helps catch issues early

---

## Debugging Toolkit

**Essential Tools Used:**
1. **Browser DevTools** - Network tab, Application tab, Console
2. **AWS CloudWatch** - Lambda logs, API Gateway logs, Custom metrics
3. **Postman / cURL** - Test API endpoints directly
4. **WebSocket Test Clients** - Test WebSocket connections independently

---

## Conclusion

Deploying to CloudFront taught me that **production is a different world**. The issues I faced—authentication cookies, WebRTC connections, WebSocket stability, and email permissions—were all invisible in local development but critical in production.

The most important lesson: **Always have a staging environment** that mirrors production. Testing on CloudFront staging first would have caught these issues before they affected users.

Week 11 was tough, but debugging these production issues gave me invaluable experience in:
- Production troubleshooting methodology
- CDN behavior and configuration
- Real-time communication infrastructure
- AWS service integration in production

These lessons will stay with me throughout my career in cloud engineering.

---

## Related Posts

- [Building Serverless Applications with AWS Lambda and DynamoDB](../3.1-blog1/)
- [Real-time Voice Communication with WebRTC on AWS](../3.2-blog2/)

---

*Written during my 12-week internship at FCJ (April 17 - July 10, 2026) as part of the AI Meeting Workforce Platform project.*
