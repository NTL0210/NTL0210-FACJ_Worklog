---
title: "Blog Posts"
date: 2025-01-15
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

# Blog Posts

During my 12-week internship at FCJ, I documented key learnings and experiences through blog posts. These posts share insights about AWS Serverless architecture, real-time communication challenges, and production deployment experiences.

---

## Blog 1: Building Serverless Applications with AWS Lambda and DynamoDB

{{< figure src="/NTL0210-FACJ_Worklog/images/blog1.png" title="AWS Serverless Architecture Overview" >}}

During my 12-week internship at FCJ, I built a production-ready serverless application using AWS Lambda and DynamoDB. This post shares the architectural decisions, design patterns, and best practices I learned while implementing a multi-tenant AI Meeting Workforce Platform.

### Why Serverless?

Serverless architecture offers several compelling benefits:
- **Zero server management**: Focus on code, not infrastructure
- **Automatic scaling**: Handle traffic spikes without manual intervention
- **Pay-per-use pricing**: Only pay for actual compute time
- **High availability**: Built-in redundancy across multiple availability zones

For an internship project with unpredictable usage patterns and limited budget, serverless was the perfect choice.

### DynamoDB Single-Table Design

One of the most important architectural decisions was using a **single-table design** for DynamoDB. Instead of creating separate tables for each entity, everything lives in one table with this structure:

```
PK                    SK                      Attributes
---------------------------------------------------------
WORKSPACE#123         METADATA#               name, createdAt, ...
WORKSPACE#123         USER#alice              role, joinedAt, ...
WORKSPACE#123         MEETING#abc             title, date, ...
WORKSPACE#123         TASK#xyz                title, assignee, ...
```

**Benefits:**
- Better performance: Fetch related data in a single query
- Lower cost: Fewer read/write operations
- Simplified access patterns: All data organized by workspace
- Easier multi-tenancy: Natural isolation between workspaces

### Key Challenges & Solutions

**Challenge 1: Cold Starts**
- Problem: First request after idle period took 2-3 seconds
- Solution: Reduced Lambda package size, used AWS SDK v3, implemented connection pooling
- Result: Cold starts reduced to < 1 second

**Challenge 2: Multi-Tenant Data Isolation**
- Problem: Risk of data leaks between workspaces
- Solution: Strict validation in every Lambda function, workspace ID in every DynamoDB key
- Result: Zero data leakage incidents across all workspaces

**Challenge 3: Cost Management**
- Problem: DynamoDB on-demand pricing unpredictability
- Solution: Implemented caching layer, optimized queries using GSIs
- Result: Kept monthly costs under $15

### Performance Optimization

**Lambda Optimization:**
```javascript
// Before: 2.5s cold start
import AWS from 'aws-sdk';

// After: <1s cold start
import { DynamoDBClient } from '@aws-sdk/client-dynamodb';
import { DynamoDBDocumentClient } from '@aws-sdk/lib-dynamodb';
```

**Connection Reuse:**
```javascript
// Global scope - reused across invocations
const dynamoClient = DynamoDBDocumentClient.from(
  new DynamoDBClient({ region: 'ap-southeast-1' })
);

export const handler = async (event) => {
  // Use existing connection
  const result = await dynamoClient.send(new GetCommand({...}));
};
```

### Key Takeaways

1. **Single-table design** is powerful but requires careful planning
2. **AWS SDK v3** significantly reduces cold start times
3. **Connection pooling** is essential for production Lambda functions
4. **Multi-tenancy** requires strict validation at every layer
5. **Serverless** is perfect for unpredictable workloads with limited budget

Building this serverless platform taught me that AWS Lambda and DynamoDB aren't just buzzwords—they're production-ready tools that can handle real-world applications when architected properly.

---

## Blog 2: Real-time Voice Communication with WebRTC on AWS

{{< figure src="/NTL0210-FACJ_Worklog/images/blog2.png" title="WebRTC Voice Communication Architecture" >}}

One of the most challenging features I implemented during my FCJ internship was real-time voice communication using WebRTC. This post shares the technical journey of building a Discord-like voice chat system on AWS infrastructure.

### What is WebRTC?

**WebRTC (Web Real-Time Communication)** is a browser technology that enables:
- **Peer-to-peer audio/video** streaming
- **Low latency**: <100ms for most connections
- **No plugins required**: Built into modern browsers
- **NAT traversal**: Works behind firewalls and routers

Perfect for building voice chat, video conferencing, or screen sharing features.

### Architecture Overview

```
┌─────────────┐         Signaling         ┌─────────────┐
│   Client A  │◄─────────────────────────►│   Client B  │
│  (Browser)  │     WebSocket/Socket.io    │  (Browser)  │
└─────────────┘                            └─────────────┘
       │                                          │
       │         ┌──────────────────┐            │
       └────────►│ Signaling Server │◄───────────┘
                 │   (Express.js)   │
                 │   on AWS Lambda  │
                 └──────────────────┘
                          │
       ┌──────────────────┴──────────────────┐
       │                                     │
┌──────▼────────┐                   ┌───────▼──────┐
│  STUN Server  │                   │ TURN Server  │
│ (Public ICE)  │                   │  (Relay if   │
│               │                   │  P2P fails)  │
└───────────────┘                   └──────────────┘

After signaling complete:
┌─────────────┐  Direct P2P Audio   ┌─────────────┐
│   Client A  │◄──────────────────►│   Client B  │
│  (Browser)  │   (No server load)  │  (Browser)  │
└─────────────┘                     └─────────────┘
```

### The Signaling Server

WebRTC requires a **signaling server** to exchange connection information (SDP offers/answers and ICE candidates). I built this using Express.js and Socket.io:

```javascript
// Signaling Server on AWS Lambda
import express from 'express';
import { Server as SocketIOServer } from 'socket.io';
import { createServer } from 'http';

const app = express();
const httpServer = createServer(app);
const io = new SocketIOServer(httpServer, {
  cors: { origin: '*' },
  transports: ['websocket', 'polling']
});

io.on('connection', (socket) => {
  console.log('User connected:', socket.id);

  // Join voice channel
  socket.on('join-channel', ({ channelId, userId }) => {
    socket.join(channelId);
    
    // Notify others in the channel
    socket.to(channelId).emit('user-joined', { userId, socketId: socket.id });
  });

  // Forward WebRTC offer
  socket.on('webrtc-offer', ({ offer, to }) => {
    io.to(to).emit('webrtc-offer', {
      offer,
      from: socket.id
    });
  });

  // Forward WebRTC answer
  socket.on('webrtc-answer', ({ answer, to }) => {
    io.to(to).emit('webrtc-answer', {
      answer,
      from: socket.id
    });
  });

  // Forward ICE candidates
  socket.on('ice-candidate', ({ candidate, to }) => {
    io.to(to).emit('ice-candidate', {
      candidate,
      from: socket.id
    });
  });

  // Handle disconnect
  socket.on('disconnect', () => {
    console.log('User disconnected:', socket.id);
  });
});
```

### Client-Side WebRTC Implementation

**Step 1: Create Peer Connection**
```javascript
const configuration = {
  iceServers: [
    { urls: 'stun:stun.l.google.com:19302' }, // Free STUN server
    { urls: 'stun:stun1.l.google.com:19302' }
  ]
};

const peerConnection = new RTCPeerConnection(configuration);

// Get local audio stream
const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
stream.getTracks().forEach(track => {
  peerConnection.addTrack(track, stream);
});
```

**Step 2: Handle ICE Candidates**
```javascript
peerConnection.onicecandidate = (event) => {
  if (event.candidate) {
    socket.emit('ice-candidate', {
      candidate: event.candidate,
      to: remotePeerId
    });
  }
};
```

**Step 3: Create Offer (Caller)**
```javascript
const offer = await peerConnection.createOffer();
await peerConnection.setLocalDescription(offer);

socket.emit('webrtc-offer', {
  offer: peerConnection.localDescription,
  to: remotePeerId
});
```

**Step 4: Handle Answer (Receiver)**
```javascript
socket.on('webrtc-offer', async ({ offer, from }) => {
  await peerConnection.setRemoteDescription(new RTCSessionDescription(offer));
  
  const answer = await peerConnection.createAnswer();
  await peerConnection.setLocalDescription(answer);
  
  socket.emit('webrtc-answer', {
    answer: peerConnection.localDescription,
    to: from
  });
});
```

**Step 5: Play Remote Audio**
```javascript
peerConnection.ontrack = (event) => {
  const audioElement = document.createElement('audio');
  audioElement.srcObject = event.streams[0];
  audioElement.autoplay = true;
  document.body.appendChild(audioElement);
};
```

### Challenges Faced & Solutions

**Challenge 1: NAT Traversal Failures**
- **Problem**: Some users behind strict firewalls couldn't connect
- **Root Cause**: Direct P2P connection failed due to symmetric NAT
- **Solution**: Implemented TURN server for relay fallback
- **Result**: 99% connection success rate

**Challenge 2: CloudFront WebSocket Issues**
- **Problem**: WebSocket connections unstable through CloudFront CDN
- **Root Cause**: CloudFront default timeout settings
- **Solution**: 
  - Configured WebSocket timeout to 3600 seconds
  - Implemented ping/pong heartbeat mechanism
  - Added automatic reconnection logic
- **Result**: Stable connections with automatic recovery

**Challenge 3: Audio Quality Issues**
- **Problem**: Users reported choppy audio or echo
- **Root Cause**: Network bandwidth variations and hardware echo
- **Solution**:
  - Enabled Opus codec with adaptive bitrate
  - Implemented echo cancellation in browser
  - Added network quality indicators
- **Result**: Clear audio quality on most connections

**Challenge 4: Multiple Peer Management**
- **Problem**: Managing WebRTC connections with 5+ users in a channel
- **Root Cause**: Each user needs peer connection with every other user (N² complexity)
- **Solution**: 
  - Created PeerManager class to handle connection lifecycle
  - Implemented automatic cleanup on disconnect
  - Added connection state monitoring
- **Result**: Smooth experience with up to 10 concurrent users

### Performance Optimizations

**1. Lazy Loading Audio Streams**
```javascript
// Only create audio stream when user joins voice channel
const startVoice = async () => {
  if (!localStream) {
    localStream = await navigator.mediaDevices.getUserMedia({ audio: true });
  }
};
```

**2. Efficient ICE Candidate Gathering**
```javascript
// Batch ICE candidates instead of sending individually
const candidateQueue = [];
let candidateTimer = null;

peerConnection.onicecandidate = (event) => {
  if (event.candidate) {
    candidateQueue.push(event.candidate);
    
    if (!candidateTimer) {
      candidateTimer = setTimeout(() => {
        socket.emit('ice-candidates-batch', {
          candidates: candidateQueue,
          to: remotePeerId
        });
        candidateQueue.length = 0;
        candidateTimer = null;
      }, 100); // Batch within 100ms
    }
  }
};
```

**3. Connection State Monitoring**
```javascript
peerConnection.onconnectionstatechange = () => {
  switch (peerConnection.connectionState) {
    case 'connected':
      console.log('✅ Peer connected');
      break;
    case 'disconnected':
      console.log('⚠️ Peer disconnected, attempting reconnect...');
      attemptReconnection();
      break;
    case 'failed':
      console.log('❌ Connection failed');
      cleanupConnection();
      break;
  }
};
```

### Key Lessons Learned

1. **WebRTC is P2P**: Signaling server only for handshake, audio flows directly between browsers
2. **STUN/TURN are essential**: Public STUN for most cases, TURN server for strict firewalls
3. **NAT traversal is hard**: About 10-20% of connections need TURN relay
4. **Quality varies by network**: Implement adaptive bitrate and quality indicators
5. **Connection management is complex**: Proper cleanup prevents memory leaks
6. **CloudFront needs configuration**: Default settings don't work well with WebSocket

### Deployment Considerations

**Infrastructure Choices:**
- **Signaling Server**: Express.js on AWS Lambda (cost-effective, scalable)
- **STUN Server**: Public servers (stun.l.google.com) - Free
- **TURN Server**: Would need dedicated EC2 instance for high traffic ($$)

**Cost Analysis:**
- **Lambda**: ~$2-5/month for signaling server
- **API Gateway WebSocket**: ~$1-2/month
- **CloudFront**: ~$2-5/month for static assets
- **TURN Server**: Not implemented (would be ~$20-50/month for EC2)

Total infrastructure cost for voice chat: **~$5-12/month** (without TURN)

### Conclusion

Building real-time voice communication with WebRTC was one of the most technically challenging and rewarding parts of my internship. The combination of browser APIs, WebSocket signaling, and AWS infrastructure created a powerful voice chat system comparable to Discord.

The key insight: **WebRTC is powerful but requires careful orchestration**. The browser handles the heavy lifting (audio encoding, network traversal), but you must implement robust signaling, connection management, and error recovery.

For anyone building similar features, start simple (2-peer connection), test thoroughly across different networks, and always have fallback mechanisms for when P2P fails.

---

## Blog 3: Debugging Production Issues on CloudFront CDN

{{< figure src="/NTL0210-FACJ_Worklog/images/blog3.png" title="CloudFront Debugging Process" >}}

Week 11 of my FCJ internship was intense—after deploying my serverless application to CloudFront, multiple production issues emerged. This post documents the debugging journey and lessons learned from production deployment.

### The Deployment Scenario

After 10 weeks of local development, it was time to deploy to production:
- **Frontend**: Next.js static site on CloudFront + S3
- **Backend**: Express.js Lambda functions behind API Gateway
- **Real-time**: WebRTC signaling server on separate Lambda
- **Authentication**: AWS Cognito with JWT tokens

Everything worked perfectly in local development (`localhost:3000`). Then I deployed to CloudFront...

### The Issues Cascade

**Four Critical Production Errors:**
1. ❌ **Login fails** - Users cannot authenticate
2. ❌ **Voice chat fails** - WebRTC connections don't establish
3. ❌ **Text chat unstable** - Messages drop or delay
4. ❌ **Workspace invitations fail** - Email invites not sending

Let's debug each one.

---

### Issue 1: Login Authentication Failure

**Symptom:**
```
POST /auth/login → 200 OK
But user still sees "Unauthorized" on protected routes
Browser console: "No valid auth token found"
```

**Initial Hypothesis:** JWT token not being saved correctly.

**Investigation:**
```javascript
// Checked browser DevTools → Application → Cookies
// Result: Cookie exists on localhost but NOT on CloudFront domain
```

**Root Cause Analysis:**

1. **Backend was setting cookie with domain=localhost**
```javascript
// This worked in development but failed in production
res.cookie('auth_token', token, {
  httpOnly: true,
  secure: true,
  domain: 'localhost' // ❌ Wrong for production!
});
```

2. **CloudFront domain mismatch:**
   - Production URL: `https://d1234abcd.cloudfront.net`
   - Cookie domain: `localhost`
   - Browser: "These don't match, reject the cookie"

3. **CORS headers incomplete:**
```javascript
// Backend allowed origin but didn't allow credentials
app.use(cors({
  origin: 'https://d1234abcd.cloudfront.net',
  credentials: false // ❌ Should be true!
}));
```

**The Fix:**

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

// backend/server.js
app.use(cors({
  origin: process.env.FRONTEND_URL,
  credentials: true // ✅ Allow credentials
}));
```

**CloudFront Configuration:**
```yaml
# Allowed headers in CloudFront behavior
AllowedHeaders:
  - Authorization
  - Cookie
  - Content-Type
AllowCredentials: true
```

**Result:** ✅ Login works, cookies persist across requests

---

### Issue 2: Voice Chat WebRTC Failure

**Symptom:**
```
User clicks "Join Voice Channel"
Loading spinner appears... forever
Browser console: "WebSocket connection failed"
```

**Investigation:**

1. **Checked signaling server logs:**
```bash
CloudWatch Logs → /aws/lambda/signaling-server
Error: "Connection timeout after 30 seconds"
```

2. **Tested WebSocket connection manually:**
```javascript
const ws = new WebSocket('wss://api.example.com/signaling');
ws.onerror = (err) => console.log(err);
// Error: "WebSocket is closed before the connection is established"
```

**Root Cause Analysis:**

1. **API Gateway WebSocket timeout:**
   - Default timeout: 10 minutes
   - CloudFront timeout: 30 seconds (overriding!)
   - WebSocket needs long-lived connection

2. **Missing WebSocket upgrade headers:**
```
CloudFront doesn't forward:
- Connection: Upgrade
- Upgrade: websocket
```

3. **Incorrect origin for STUN/TURN:**
```javascript
// Frontend was using localhost STUN config in production
const iceServers = [
  { urls: 'stun:localhost:3478' } // ❌ Wrong!
];
```

**The Fix:**

**1. CloudFront WebSocket Configuration:**
```yaml
# CloudFront behavior for /signaling path
PathPattern: /signaling*
TargetOriginId: ApiGatewayWebSocket
ViewerProtocolPolicy: https-only
AllowedMethods: [GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE]
ForwardedValues:
  QueryString: true
  Headers:
    - Sec-WebSocket-Key
    - Sec-WebSocket-Version
    - Sec-WebSocket-Protocol
    - Sec-WebSocket-Accept
  Cookies:
    Forward: all
```

**2. API Gateway Settings:**
```javascript
// Extend timeout for WebSocket connections
const apiGatewayConfig = {
  timeout: 3600000, // 1 hour in milliseconds
  keepAlive: true
};
```

**3. Frontend STUN/TURN Configuration:**
```javascript
// Use public STUN servers in production
const iceServers = process.env.NODE_ENV === 'production'
  ? [
      { urls: 'stun:stun.l.google.com:19302' },
      { urls: 'stun:stun1.l.google.com:19302' }
    ]
  : [{ urls: 'stun:localhost:3478' }];
```

**4. Implement Heartbeat Ping/Pong:**
```javascript
// Keep WebSocket alive through CloudFront
socket.on('connect', () => {
  setInterval(() => {
    socket.emit('ping');
  }, 25000); // Ping every 25 seconds (before 30s timeout)
});

// Server responds to keep connection alive
socket.on('ping', () => {
  socket.emit('pong');
});
```

**Result:** ✅ Voice chat connects reliably, WebSocket stays alive

---

### Issue 3: Text Chat Message Instability

**Symptom:**
```
Messages send successfully sometimes
Other times: "Message failed to send"
Random disconnections every 2-3 minutes
```

**Investigation:**

```javascript
// Browser DevTools → Network → WS
// WebSocket connection closes unexpectedly
// Close code: 1006 (Abnormal Closure)
```

**Root Cause:**

1. **No automatic reconnection logic**
2. **CloudFront closing idle WebSocket connections**
3. **No queuing for failed messages**

**The Fix:**

```javascript
// Implement automatic reconnection with exponential backoff
class ReconnectingWebSocket {
  constructor(url) {
    this.url = url;
    this.reconnectAttempts = 0;
    this.maxReconnectAttempts = 5;
    this.reconnectDelay = 1000; // Start with 1 second
    
    this.connect();
  }

  connect() {
    this.socket = io(this.url, {
      transports: ['websocket', 'polling'],
      reconnection: true,
      reconnectionAttempts: this.maxReconnectAttempts,
      reconnectionDelay: this.reconnectDelay,
      reconnectionDelayMax: 10000 // Max 10 seconds
    });

    this.socket.on('connect', () => {
      console.log('✅ Connected to chat server');
      this.reconnectAttempts = 0;
      this.reconnectDelay = 1000;
      
      // Resend queued messages
      this.flushMessageQueue();
    });

    this.socket.on('disconnect', (reason) => {
      console.log('⚠️ Disconnected:', reason);
      
      if (reason === 'io server disconnect') {
        // Server kicked us, reconnect manually
        this.reconnect();
      }
    });
  }

  reconnect() {
    if (this.reconnectAttempts < this.maxReconnectAttempts) {
      this.reconnectAttempts++;
      
      const delay = Math.min(
        this.reconnectDelay * Math.pow(2, this.reconnectAttempts),
        10000
      );
      
      console.log(`Reconnecting in ${delay}ms...`);
      
      setTimeout(() => this.connect(), delay);
    }
  }

  send(message) {
    if (this.socket.connected) {
      this.socket.emit('message', message);
    } else {
      // Queue message if disconnected
      this.messageQueue.push(message);
    }
  }
}
```

**Result:** ✅ Messages deliver reliably, automatic reconnection works

---

### Issue 4: Workspace Invitation Email Failures

**Symptom:**
```
User sends workspace invitation
Frontend shows "Invitation sent" ✅
But recipient never receives email 📭
```

**Investigation:**

```bash
# Check Lambda logs
CloudWatch → /aws/lambda/send-invitation
Error: "Missing AWS credentials"

# Check IAM role permissions
Lambda execution role doesn't have SES:SendEmail permission ❌
```

**Root Cause:**

Lambda function couldn't send emails through AWS SES due to missing IAM permissions.

**The Fix:**

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

```javascript
// backend/invitations.js
import { SESClient, SendEmailCommand } from '@aws-sdk/client-ses';

const sesClient = new SESClient({ region: 'ap-southeast-1' });

export const sendInvitation = async (email, workspaceName, inviteLink) => {
  const params = {
    Source: 'noreply@example.com',
    Destination: { ToAddresses: [email] },
    Message: {
      Subject: { Data: `You're invited to ${workspaceName}` },
      Body: {
        Html: {
          Data: `
            <h2>Join ${workspaceName} on AI Meeting Platform</h2>
            <p>Click the link below to accept the invitation:</p>
            <a href="${inviteLink}">Accept Invitation</a>
          `
        }
      }
    }
  };

  try {
    await sesClient.send(new SendEmailCommand(params));
    console.log('✅ Invitation email sent to', email);
  } catch (error) {
    console.error('❌ Failed to send email:', error);
    throw error;
  }
};
```

**Result:** ✅ Invitation emails delivered successfully

---

### Key Lessons Learned

**1. Local Development ≠ Production**
- Always test in staging environment that mirrors production
- CloudFront has different behaviors than localhost
- CORS, cookies, and WebSockets behave differently

**2. Cookie Configuration Matters**
- `domain`, `sameSite`, `secure` settings are critical
- Production needs `sameSite: 'none'` for cross-origin cookies
- Always set `credentials: true` in CORS if using cookies

**3. WebSocket Through CDN is Tricky**
- CloudFront has 30-second idle timeout by default
- Implement ping/pong heartbeat to keep connections alive
- Forward WebSocket upgrade headers explicitly
- Configure separate cache behavior for WebSocket paths

**4. Always Implement Reconnection Logic**
- Networks are unreliable, plan for disconnections
- Exponential backoff prevents server overload
- Queue messages during reconnection
- Show connection status to users

**5. IAM Permissions Are Not Optional**
- Every AWS service needs explicit permissions
- Test all integrations in production-like environment
- Monitor CloudWatch logs for permission errors

**6. Monitor Everything in Production**
- CloudWatch logs saved debugging time
- Real-time monitoring helps catch issues early
- Set up alarms for critical failures

### Debugging Toolkit

**Essential Tools Used:**
1. **Browser DevTools**
   - Network tab for API calls
   - Application tab for cookies
   - Console for errors

2. **AWS CloudWatch**
   - Lambda logs for backend errors
   - API Gateway logs for requests
   - Custom metrics for monitoring

3. **Postman / cURL**
   - Test API endpoints directly
   - Verify headers and responses

4. **WebSocket Test Clients**
   - Test WebSocket connections independently
   - Verify signaling server behavior

### Conclusion

Deploying to CloudFront taught me that **production is a different world**. The issues I faced—authentication cookies, WebRTC connections, WebSocket stability, and email permissions—were all invisible in local development but critical in production.

The most important lesson: **Always have a staging environment** that mirrors production. Testing on CloudFront staging first would have caught these issues before they affected users.

Week 11 was tough, but debugging these production issues gave me invaluable experience in:
- Production troubleshooting methodology
- CDN behavior and configuration
- Real-time communication infrastructure
- AWS service integration in production

These lessons will stay with me throughout my career in cloud engineering.

---

## Conclusion

These three blog posts document key technical challenges from my 12-week FCJ internship:

1. **Blog 1**: Serverless architecture with AWS Lambda and DynamoDB single-table design
2. **Blog 2**: Real-time voice communication using WebRTC, signaling servers, and NAT traversal
3. **Blog 3**: Production debugging on CloudFront CDN, fixing cookies, WebSockets, and permissions

Each challenge taught me that building production-ready applications requires more than just writing code—it requires understanding infrastructure, debugging methodology, and resilience engineering.

**Key Takeaways:**
- Serverless architecture is powerful for cost-effective scalability
- Real-time communication needs careful orchestration and fallback mechanisms
- Production deployment reveals issues invisible in development
- AWS offers amazing tools, but each needs proper configuration
- Comprehensive logging and monitoring are essential for debugging

Thank you for reading! These experiences shaped my understanding of modern cloud architecture and prepared me for building production systems at scale.

---

*These blog posts were written during my 12-week internship at FCJ (April 17 - July 10, 2026) as part of the AI Meeting Workforce Platform project. For more details, see the [complete worklog](/) and [project proposal](/2-proposal/).*
