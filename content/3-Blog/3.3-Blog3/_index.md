---
title: "Debugging Production Issues on CloudFront CDN"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Debugging Production Issues on CloudFront CDN

{{< figure src="/NTL0210-FACJ_Worklog/images/blog3.png" title="CloudFront Deployment and Debugging Process" >}}

## Introduction

Week 11 of my internship was intense. After deploying my application to AWS CloudFront, I encountered multiple critical issues: login failures, voice chat errors, text messaging problems, and workspace invitation bugs. This blog post documents the debugging journey, root cause analysis, and solutions that taught me the difference between local development and production deployment.

## The Deployment

After 10 weeks of development and local testing, everything worked perfectly on `localhost`. The application had:
- User authentication with AWS Cognito
- Real-time voice chat with WebRTC
- Text messaging with WebSocket
- Workspace member invitations

Confident in my code, I deployed to CloudFront CDN for global distribution and HTTPS support. That's when everything broke.

## Issue 1: Login Failures

### The Problem

Users couldn't log in after CloudFront deployment. The login form submitted successfully, but authentication tokens weren't persisted. Refresh the page? Logged out again.

### Symptoms Observed:
```
Browser Console:
- "CORS policy blocked request"
- "Set-Cookie was blocked by policy"
- Authentication token: null

Network Tab:
- Status 200 OK from auth endpoint
- Set-Cookie header present in response
- Cookie not saved in browser
```

### Investigation Process

**Step 1: Check CORS Configuration**
```javascript
// API Gateway CORS - Looked correct
headers: {
  'Access-Control-Allow-Origin': 'https://d123xyz.cloudfront.net',
  'Access-Control-Allow-Credentials': 'true'
}
```

**Step 2: Inspect Cookie Settings**
```javascript
// Original code
res.cookie('authToken', token, {
  httpOnly: true,
  secure: true,
  sameSite: 'strict'  // <- THE PROBLEM!
});
```

### Root Cause Analysis

The issue was **SameSite=Strict** cookie policy combined with CloudFront domain mismatch:

1. Frontend: `https://d123xyz.cloudfront.net`
2. API: `https://api.myapp.com`
3. Cookie with `SameSite=Strict` rejected cross-site requests
4. CloudFront adds another layer of domain complexity

### Solution Implemented

```javascript
res.cookie('authToken', token, {
  httpOnly: true,
  secure: true,
  sameSite: 'none',           // Changed to 'none' for cross-site
  domain: '.myapp.com',       // Wildcard domain
  path: '/',
  maxAge: 7 * 24 * 60 * 60 * 1000  // 7 days
});
```

**Additional fixes:**
- Updated CORS to allow CloudFront distribution domain
- Configured CloudFront behaviors to not cache auth endpoints
- Added proper `Vary` headers

### Lesson Learned

**Cookie security is complex with CDNs.** What works locally doesn't always work in production with different domains and CDN layers.

## Issue 2: Voice Chat Errors

### The Problem

Voice chat completely failed on CloudFront. Users could join channels but no audio transmission occurred.

### Symptoms Observed:

```
WebRTC Error:
- "Failed to gather ICE candidates"
- "Connection state: failed"
- Signaling messages not reaching peers
```

### Investigation Process

**Step 1: Check Signaling Server**
```bash
# Signaling server logs
WebSocket connection failed: 403 Forbidden
Origin: https://d123xyz.cloudfront.net
```

**Step 2: Trace WebRTC Connection**
```javascript
peerConnection.oniceconnectionstatechange = () => {
  console.log('ICE state:', peerConnection.iceConnectionState);
  // State stuck at "checking", never reaches "connected"
};
```

### Root Cause Analysis

Multiple issues compounded:

1. **Signaling Server Accessibility**: Signaling server behind CloudFront with wrong WebSocket configuration
2. **STUN/TURN Configuration**: ICE servers configured with localhost addresses
3. **Mixed Content**: CloudFront uses HTTPS, signaling server was HTTP

### Solution Implemented

**Fix 1: Deploy Signaling Server Separately**
```javascript
// Don't route signaling through CloudFront
const signalingServer = 'wss://signal.myapp.com';  // Dedicated domain

// Client connection
const socket = io(signalingServer, {
  transports: ['websocket'],
  secure: true
});
```

**Fix 2: Update ICE Server Configuration**
```javascript
const iceServers = [
  // Public STUN servers
  { urls: 'stun:stun.l.google.com:19302' },
  { urls: 'stun:stun1.l.google.com:19302' },
  
  // Production TURN server (not localhost!)
  {
    urls: 'turn:turn.myapp.com:3478',
    username: getTurnUsername(),
    credential: getTurnCredential()
  }
];
```

**Fix 3: Ensure HTTPS Everywhere**
- Signaling server: WSS (secure WebSocket)
- TURN server: TLS enabled
- STUN: Works with regular STUN (no security needed)

### Lesson Learned

**Real-time communication needs special CDN consideration.** WebSocket connections, STUN/TURN servers, and signaling paths must be configured for production domains, not localhost.

## Issue 3: Text Chat Message Delivery

### The Problem

Text messages appeared inconsistently. Sometimes messages arrived, sometimes they didn't. Message order was random.

### Symptoms Observed:
```
User A sends: "Hello"
User B sees nothing
User A sends: "How are you?"
User B sees: "How are you?" then "Hello" (wrong order!)
```

### Investigation Process

**Step 1: Check WebSocket Connection**
```javascript
socket.on('connect', () => {
  console.log('Connected:', socket.id);
});

socket.on('disconnect', (reason) => {
  console.log('Disconnected:', reason);
  // Seeing frequent "transport close" disconnects
});
```

**Step 2: Monitor Message Flow**
```javascript
// Client sends
socket.emit('send-message', { channel, text, timestamp });

// Server receives and broadcasts
socket.on('send-message', (data) => {
  console.log('Received message:', data);
  io.to(data.channel).emit('new-message', data);
});
```

### Root Cause Analysis


The issues were:

1. **WebSocket Instability Through CloudFront**: CloudFront timeout settings too aggressive
2. **No Reconnection Logic**: Client didn't handle disconnects gracefully
3. **Missing Message Queue**: No retry mechanism for failed sends
4. **No Acknowledgments**: Can't confirm message delivery

### Solution Implemented

**Fix 1: Implement Robust Reconnection**
```javascript
const socket = io(signalingServer, {
  reconnection: true,
  reconnectionDelay: 1000,
  reconnectionDelayMax: 5000,
  reconnectionAttempts: Infinity
});

socket.on('reconnect', (attemptNumber) => {
  console.log('Reconnected after', attemptNumber, 'attempts');
  // Re-sync messages
  fetchMissedMessages();
});
```

**Fix 2: Add Message Queue with Acknowledgments**
```javascript
// Client side
const messageQueue = [];

function sendMessage(message) {
  const msgId = generateId();
  messageQueue.push({ id: msgId, message, status: 'pending' });
  
  socket.emit('send-message', { msgId, ...message }, (ack) => {
    if (ack.success) {
      markMessageSent(msgId);
    } else {
      retryMessage(msgId);
    }
  });
}

// Server side
socket.on('send-message', (data, callback) => {
  try {
    // Save to database
    await saveMessage(data);
    
    // Broadcast to channel
    io.to(data.channel).emit('new-message', data);
    
    // Acknowledge success
    callback({ success: true });
  } catch (error) {
    callback({ success: false, error: error.message });
  }
});
```

**Fix 3: Client-Side Message Ordering**
```javascript
const messageCache = new Map();

socket.on('new-message', (message) => {
  messageCache.set(message.id, message);
  
  // Sort by timestamp before displaying
  const sortedMessages = Array.from(messageCache.values())
    .sort((a, b) => a.timestamp - b.timestamp);
  
  displayMessages(sortedMessages);
});
```

### Lesson Learned

**Real-time messaging needs reliability layers.** Network issues are common in production. Implement reconnection, acknowledgments, and message queues from the start.

## Issue 4: Workspace Invitation Failures

### The Problem

Workspace invitation emails weren't being sent. The invitation feature worked perfectly locally but failed silently in production.

### Symptoms Observed:
```
Admin clicks "Invite Member"
- Frontend shows success message
- Backend logs show "Email sent"
- User never receives email
- No errors in logs
```

### Investigation Process

**Step 1: Check Email Service**
```javascript
// Using AWS SES (Simple Email Service)
const ses = new AWS.SES({ region: 'us-east-1' });

await ses.sendEmail({
  Source: 'noreply@myapp.com',
  Destination: { ToAddresses: [inviteEmail] },
  Message: { ... }
}).promise();

// No errors thrown, but email not delivered
```

**Step 2: Check SES Console**
```
SES Dashboard:
- Emails in "Pending" status
- Error: "Email address not verified"
- Account in "Sandbox mode"
```

### Root Cause Analysis

AWS SES was in **sandbox mode**, which restricts:
- Can only send TO verified email addresses
- Can only send FROM verified email addresses  
- Limited to 200 emails per day
- All recipients must be manually verified

In development, I only tested with my own verified email. In production, users have arbitrary email addresses that aren't verified.

### Solution Implemented

**Fix 1: Request SES Production Access**

- Submitted request to AWS Support
- Explained use case (workspace invitations)
- Provided email sending policies
- Approved within 24 hours

**Fix 2: Add Proper Error Handling**
```javascript
async function sendInvitationEmail(email, inviteLink) {
  try {
    const result = await ses.sendEmail({
      Source: 'noreply@myapp.com',
      Destination: { ToAddresses: [email] },
      Message: {
        Subject: { Data: 'Workspace Invitation' },
        Body: {
          Html: { Data: generateEmailHTML(inviteLink) }
        }
      }
    }).promise();
    
    // Log MessageId for tracking
    console.log('Email sent:', result.MessageId);
    
    // Save to database for audit
    await logEmailSent(email, result.MessageId);
    
    return { success: true, messageId: result.MessageId };
    
  } catch (error) {
    console.error('Email send failed:', error);
    
    // Return error to frontend
    throw new Error(`Failed to send invitation: ${error.message}`);
  }
}
```

**Fix 3: Add Invitation Link Validation**
```javascript
// Original code - used localhost URL
const inviteLink = `http://localhost:3000/invite/${token}`;

// Fixed code - use actual domain
const inviteLink = `https://${process.env.DOMAIN}/invite/${token}`;

// Environment variables
// Local: DOMAIN=localhost:3000
// Production: DOMAIN=myapp.com
```

**Fix 4: Implement Retry Mechanism**
```javascript
async function sendInvitationWithRetry(email, inviteLink, maxRetries = 3) {
  let lastError;
  
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      return await sendInvitationEmail(email, inviteLink);
    } catch (error) {
      lastError = error;
      console.log(`Attempt ${attempt} failed, retrying...`);
      await sleep(1000 * attempt); // Exponential backoff
    }
  }
  
  throw new Error(`Failed after ${maxRetries} attempts: ${lastError.message}`);
}
```

### Lesson Learned

**Test with production-like configuration.** Sandbox mode, localhost URLs, and verified emails hide real production issues.

## General Debugging Strategies

### 1. Comprehensive Logging
```javascript
// Structured logging with context
function log(level, message, context = {}) {
  console.log(JSON.stringify({
    timestamp: new Date().toISOString(),
    level,
    message,
    ...context,
    environment: process.env.NODE_ENV
  }));
}

// Usage
log('error', 'Cookie not set', {
  userId: user.id,
  sameSite: cookieConfig.sameSite,
  domain: cookieConfig.domain
});
```

### 2. Feature Flags
```javascript
// Gradually enable features in production
const features = {
  voiceChat: process.env.ENABLE_VOICE === 'true',
  emailInvites: process.env.ENABLE_EMAIL === 'true'
};

if (features.voiceChat) {
  initializeVoiceChat();
}
```

### 3. Environment Parity
```javascript
// Same configuration structure for all environments
const config = {
  local: {
    domain: 'localhost:3000',
    apiUrl: 'http://localhost:4000',
    signalingUrl: 'ws://localhost:5000'
  },
  staging: {
    domain: 'staging.myapp.com',
    apiUrl: 'https://api-staging.myapp.com',
    signalingUrl: 'wss://signal-staging.myapp.com'
  },
  production: {
    domain: 'myapp.com',
    apiUrl: 'https://api.myapp.com',
    signalingUrl: 'wss://signal.myapp.com'
  }
};
```

### 4. Monitoring and Alerts

```javascript
// CloudWatch metrics
const cloudwatch = new AWS.CloudWatch();

async function recordMetric(metricName, value) {
  await cloudwatch.putMetricData({
    Namespace: 'MyApp',
    MetricData: [{
      MetricName: metricName,
      Value: value,
      Unit: 'Count',
      Timestamp: new Date()
    }]
  }).promise();
}

// Track key events
await recordMetric('LoginFailures', 1);
await recordMetric('VoiceChatConnections', 1);
await recordMetric('EmailsSent', 1);
```

## Key Learnings from Week 11

1. **Local testing is insufficient**: Always deploy to staging environment that mirrors production

2. **CDN changes everything**: Cookies, WebSockets, and CORS behave differently with CloudFront

3. **Monitoring is crucial**: Can't fix what you can't see - log everything

4. **Error handling matters**: Silent failures are the worst - always return meaningful errors

5. **Read the documentation**: AWS services have gotchas (SES sandbox mode, CloudFront WebSocket limits)

6. **Test incrementally**: Deploy one feature at a time to isolate issues

7. **Have rollback plan**: Keep previous working version ready to redeploy

## Tools That Saved Me

### Browser DevTools:
- **Network tab**: Inspect headers, timing, and failures
- **Console**: Check errors and WebRTC connection states
- **Application tab**: View cookies and localStorage

### AWS Tools:
- **CloudWatch Logs**: Centralized logging from all services
- **CloudWatch Metrics**: Track system health and usage
- **X-Ray**: Trace requests through distributed system
- **SES Console**: Monitor email delivery status

### Third-Party Tools:
- **Postman**: Test API endpoints independently
- **webrtc-internals**: Chrome's built-in WebRTC debugger (chrome://webrtc-internals)
- **Socket.io Admin UI**: Monitor WebSocket connections

## Conclusion

Week 11 was the most challenging week of my internship, but also the most educational. Debugging production issues taught me more about real-world software engineering than 10 weeks of development.

The difference between "it works on my machine" and "it works in production" is enormous. Understanding CDN behavior, cookie policies, WebSocket configuration, and AWS service limitations is crucial for deploying production-ready applications.

For anyone deploying to CloudFront or any CDN: expect things to break, plan for debugging time, and test in production-like environments early and often.

## Resources

- [AWS CloudFront Documentation](https://docs.aws.amazon.com/cloudfront/)
- [MDN: SameSite Cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie/SameSite)
- [WebRTC Troubleshooting Guide](https://webrtc.github.io/samples/)
- [AWS SES Sandbox Mode](https://docs.aws.amazon.com/ses/latest/dg/request-production-access.html)
- [Project GitHub Repository](https://github.com/NTL0210/FACJ_Worklog)

---

**Author:** Nguyễn Tấn Lộc  
**Date:** July 10, 2026  
**Tags:** CloudFront, Debugging, Production Issues, AWS, CDN, WebRTC, SES, CORS, Cookies
