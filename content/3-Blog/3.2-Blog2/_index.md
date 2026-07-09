---
title: "Real-time Voice Communication with WebRTC on AWS"
date: 2026-07-09
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Real-time Voice Communication with WebRTC on AWS

{{< figure src="/NTL0210-FACJ_Worklog/images/blog2.png" title="WebRTC Voice Communication Architecture" >}}

## Introduction

One of the most challenging aspects of my internship project was implementing real-time voice communication using WebRTC. This blog post shares the technical architecture, implementation details, and lessons learned from building a Discord-like voice chat system on AWS infrastructure.

## What is WebRTC?

**WebRTC (Web Real-Time Communication)** is an open-source technology that enables peer-to-peer audio, video, and data sharing directly between browsers without requiring plugins or additional software.

### Key WebRTC Benefits:

- **Low latency**: Direct peer-to-peer connections
- **Browser native**: Works in Chrome, Firefox, Safari, Edge
- **Secure**: Mandatory encryption for all communications
- **NAT traversal**: Works behind firewalls and routers
- **Cross-platform**: Same code works on desktop and mobile

## Architecture Overview

The voice communication system consists of three main components:

```
┌──────────────┐         ┌──────────────┐
│   Client A   │         │   Client B   │
│  (Browser)   │         │  (Browser)   │
└───────┬──────┘         └───────┬──────┘
        │                        │
        │   Signaling Messages   │
        └────────┬───────────────┘
                 │
        ┌────────▼────────┐
        │   Signaling     │
        │     Server      │
        │  (Socket.io)    │
        └─────────────────┘
                 
┌──────────────────────────────────────┐
│         STUN/TURN Servers            │
│   (ICE Candidate Discovery)          │
└──────────────────────────────────────┘
```

### Components:

1. **Signaling Server**: Exchanges connection information between peers
2. **STUN Server**: Helps discover public IP addresses
3. **TURN Server**: Relays media when direct connection fails
4. **WebRTC Peers**: Browser clients that send/receive audio

## Signaling Server Implementation

The signaling server coordinates connection setup between peers using Socket.io:

### Server Code (Express.js on AWS Lambda):

```javascript
const io = require('socket.io')(server);

io.on('connection', (socket) => {
  console.log('User connected:', socket.id);
  
  // User joins a voice channel
  socket.on('join-channel', (channelId) => {
    socket.join(channelId);
    
    // Notify others in channel
    socket.to(channelId).emit('user-joined', {
      userId: socket.id
    });
  });
  
  // Forward WebRTC offer
  socket.on('offer', (data) => {
    socket.to(data.target).emit('offer', {
      offer: data.offer,
      from: socket.id
    });
  });
  
  // Forward WebRTC answer
  socket.on('answer', (data) => {
    socket.to(data.target).emit('answer', {
      answer: data.answer,
      from: socket.id
    });
  });
  
  // Forward ICE candidates
  socket.on('ice-candidate', (data) => {
    socket.to(data.target).emit('ice-candidate', {
      candidate: data.candidate,
      from: socket.id
    });
  });
  
  // User leaves channel
  socket.on('leave-channel', (channelId) => {
    socket.to(channelId).emit('user-left', {
      userId: socket.id
    });
    socket.leave(channelId);
  });
});
```

### Key Signaling Messages:

1. **offer**: Initial connection proposal with SDP (Session Description Protocol)
2. **answer**: Response to offer with SDP
3. **ice-candidate**: Network path candidates for connection
4. **join/leave**: Channel membership management

## Client-Side WebRTC Implementation

The browser client manages WebRTC connections using the RTCPeerConnection API:

### Creating Peer Connection:
```javascript
const peerConnection = new RTCPeerConnection({
  iceServers: [
    { urls: 'stun:stun.l.google.com:19302' },
    {
      urls: 'turn:turn.example.com:3478',
      username: 'user',
      credential: 'pass'
    }
  ]
});

// Add local audio stream
const stream = await navigator.mediaDevices.getUserMedia({
  audio: {
    echoCancellation: true,
    noiseSuppression: true,
    autoGainControl: true
  },
  video: false
});

stream.getTracks().forEach(track => {
  peerConnection.addTrack(track, stream);
});

// Handle incoming audio stream
peerConnection.ontrack = (event) => {
  const remoteAudio = new Audio();
  remoteAudio.srcObject = event.streams[0];
  remoteAudio.play();
};
```

### Establishing Connection:
```javascript
// Caller creates offer
const offer = await peerConnection.createOffer();
await peerConnection.setLocalDescription(offer);
socket.emit('offer', { offer, target: remoteUserId });

// Callee receives offer and sends answer
socket.on('offer', async ({ offer, from }) => {
  await peerConnection.setRemoteDescription(offer);
  const answer = await peerConnection.createAnswer();
  await peerConnection.setLocalDescription(answer);
  socket.emit('answer', { answer, target: from });
});

// Both sides handle ICE candidates
peerConnection.onicecandidate = (event) => {
  if (event.candidate) {
    socket.emit('ice-candidate', {
      candidate: event.candidate,
      target: remoteUserId
    });
  }
};

socket.on('ice-candidate', async ({ candidate }) => {
  await peerConnection.addIceCandidate(candidate);
});
```

## STUN and TURN Servers

### STUN (Session Traversal Utilities for NAT):
- Discovers public IP address and port
- Enables direct peer-to-peer connections
- Free public STUN servers available (Google, Mozilla)

### TURN (Traversal Using Relays around NAT):
- Relays media when direct connection impossible
- Required for restrictive firewalls (10-20% of cases)
- Costs bandwidth and infrastructure

### Configuration Strategy:
```javascript
const iceServers = [
  // Try STUN first (free, direct connection)
  { urls: 'stun:stun.l.google.com:19302' },
  { urls: 'stun:stun1.l.google.com:19302' },
  
  // Fallback to TURN if needed (costs bandwidth)
  {
    urls: 'turn:turn.myapp.com:3478',
    username: generateTurnUsername(),
    credential: generateTurnCredential()
  }
];
```

## Audio Quality Optimization

Achieving good audio quality required several optimizations:

### 1. Audio Constraints:
```javascript
const audioConstraints = {
  echoCancellation: true,      // Remove echo
  noiseSuppression: true,       // Filter background noise
  autoGainControl: true,        // Normalize volume
  sampleRate: 48000,            // High quality audio
  channelCount: 1               // Mono (sufficient for voice)
};
```

### 2. Codec Selection:
```javascript
// Prefer Opus codec (best for voice)
const offer = await peerConnection.createOffer();
offer.sdp = offer.sdp.replace(
  'm=audio',
  'm=audio\r\na=fmtp:111 minptime=10;useinbandfec=1'
);
```

### 3. Bitrate Control:
```javascript
const sender = peerConnection.getSenders()[0];
const parameters = sender.getParameters();

parameters.encodings[0].maxBitrate = 128000; // 128 kbps
await sender.setParameters(parameters);
```

## Challenges & Solutions

### Challenge 1: NAT Traversal Failures
**Problem**: 15-20% of connections couldn't establish direct peer-to-peer  
**Root Cause**: Symmetric NAT and restrictive firewalls  
**Solution**:
- Implemented TURN server fallback
- Added connection state monitoring
- Automatic reconnection on failure

### Challenge 2: CloudFront Compatibility
**Problem**: WebRTC signaling didn't work through CloudFront  
**Root Cause**: WebSocket connections need special CloudFront configuration  
**Solution**:
- Deployed signaling server separately
- Used direct domain (not through CloudFront)
- Configured CORS properly

### Challenge 3: Multiple Participants
**Problem**: Mesh topology (everyone connects to everyone) doesn't scale  
**Architecture**: 
- 2 participants: 1 connection
- 5 participants: 10 connections
- 10 participants: 45 connections

**Solution** (for MVP):
- Limited channels to 10 participants
- Used mesh topology for simplicity
- Future: Implement SFU (Selective Forwarding Unit) for scalability

### Challenge 4: Mobile Browser Support
**Problem**: iOS Safari had WebRTC quirks  
**Issues**:
- Required user interaction to play audio
- Different ICE candidate behavior
- Audio context restrictions

**Solution**:
- Added explicit user permission prompts
- Implemented iOS-specific audio handling
- Tested on multiple devices

## Performance Monitoring

Monitoring WebRTC quality is crucial for user experience:

### Metrics Collected:
```javascript
setInterval(async () => {
  const stats = await peerConnection.getStats();
  
  stats.forEach(report => {
    if (report.type === 'inbound-rtp' && report.kind === 'audio') {
      console.log('Audio quality:', {
        packetsLost: report.packetsLost,
        jitter: report.jitter,
        bytesReceived: report.bytesReceived
      });
    }
  });
}, 5000);
```

### Key Metrics:
- **Packet loss**: Should be < 1%
- **Jitter**: Should be < 30ms
- **Round-trip time**: Should be < 200ms
- **Connection state**: Monitor for disconnections

## AWS Infrastructure Considerations

### Signaling Server Deployment:
- **EC2 instance**: Simple but requires management
- **Lambda + API Gateway WebSocket**: Serverless but cold starts
- **ECS Fargate**: Container-based, good balance

I chose **EC2 t3.micro** for predictable performance and always-on availability.

### TURN Server:
- **Coturn on EC2**: Open-source, cost-effective
- **AWS Media Services**: Fully managed but expensive
- **Third-party service**: Twilio, Agora (easiest but recurring cost)

I used **Coturn on t3.small EC2** to minimize costs.

## Security Considerations

### 1. Authentication:
```javascript
socket.on('connection', async (socket) => {
  const token = socket.handshake.auth.token;
  const user = await verifyJWT(token);
  
  if (!user) {
    socket.disconnect();
    return;
  }
  
  socket.userId = user.id;
});
```

### 2. Channel Access Control:
```javascript
socket.on('join-channel', async (channelId) => {
  const hasAccess = await checkChannelAccess(
    socket.userId,
    channelId
  );
  
  if (!hasAccess) {
    socket.emit('error', 'Access denied');
    return;
  }
  
  socket.join(channelId);
});
```

### 3. TURN Credentials:
- Generate temporary credentials (time-limited)
- Use HMAC for credential generation
- Rotate secrets regularly

## Key Learnings

1. **WebRTC is complex but powerful**: Worth the learning curve
2. **Always have TURN fallback**: Direct connections don't always work
3. **Monitor connection quality**: Proactive detection prevents bad UX
4. **Test on real networks**: Office WiFi isn't like home networks
5. **Start simple, scale later**: Mesh topology fine for small groups

## Conclusion

Building real-time voice communication with WebRTC was one of the most technically challenging and rewarding parts of my internship. The technology is powerful but has a steep learning curve. Understanding signaling, NAT traversal, and audio optimization is crucial for production-quality voice chat.

For anyone implementing WebRTC on AWS, my advice: start with proven libraries (simple-peer, PeerJS), understand the fundamentals before optimizing, and always plan for TURN server fallback.

## Resources

- [WebRTC Documentation](https://webrtc.org/getting-started/overview)
- [MDN WebRTC API](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API)
- [Socket.io Documentation](https://socket.io/docs/)
- [Coturn TURN Server](https://github.com/coturn/coturn)
- [Project GitHub Repository](https://github.com/NTL0210/FACJ_Worklog)

---

**Author:** Nguyễn Tấn Lộc  
**Date:** July 9, 2026  
**Tags:** WebRTC, Real-time Communication, AWS, Socket.io, Voice Chat, Peer-to-peer
