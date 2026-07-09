---
title: "Real-time Voice Communication with WebRTC on AWS"
date: 2025-01-15
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Real-time Voice Communication with WebRTC on AWS

{{< figure src="/NTL0210-FACJ_Worklog/images/blog2.png" title="WebRTC Voice Communication Architecture" >}}

One of the most challenging features I implemented during my FCJ internship was real-time voice communication using WebRTC. This post shares the technical journey of building a Discord-like voice chat system on AWS infrastructure.

---

## What is WebRTC?

**WebRTC (Web Real-Time Communication)** is a browser technology that enables:
- **Peer-to-peer audio/video** streaming
- **Low latency**: <100ms for most connections
- **No plugins required**: Built into modern browsers
- **NAT traversal**: Works behind firewalls and routers

Perfect for building voice chat, video conferencing, or screen sharing features.

---

## Architecture Overview

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

---

## The Signaling Server

WebRTC requires a **signaling server** to exchange connection information (SDP offers/answers and ICE candidates). I built this using Express.js and Socket.io on AWS Lambda.

### Key Implementation:
- Handles WebSocket connections for signaling
- Forwards ICE candidates between peers
- Manages room/channel membership
- Implements automatic reconnection logic

---

## Key Challenges & Solutions

### Challenge 1: NAT Traversal Failures
- **Problem**: Some users behind strict firewalls couldn't connect
- **Root Cause**: Direct P2P connection failed due to symmetric NAT
- **Solution**: Implemented TURN server for relay fallback
- **Result**: 99% connection success rate

### Challenge 2: CloudFront WebSocket Issues
- **Problem**: WebSocket connections unstable through CloudFront CDN
- **Root Cause**: CloudFront default timeout settings
- **Solution**: 
  - Configured WebSocket timeout to 3600 seconds
  - Implemented ping/pong heartbeat mechanism
  - Added automatic reconnection logic
- **Result**: Stable connections with automatic recovery

### Challenge 3: Audio Quality Issues
- **Problem**: Users reported choppy audio or echo
- **Root Cause**: Network bandwidth variations and hardware echo
- **Solution**:
  - Enabled Opus codec with adaptive bitrate
  - Implemented echo cancellation in browser
  - Added network quality indicators
- **Result**: Clear audio quality on most connections

### Challenge 4: Multiple Peer Management
- **Problem**: Managing WebRTC connections with 5+ users in a channel
- **Root Cause**: Each user needs peer connection with every other user (N² complexity)
- **Solution**: 
  - Created PeerManager class to handle connection lifecycle
  - Implemented automatic cleanup on disconnect
  - Added connection state monitoring
- **Result**: Smooth experience with up to 10 concurrent users

---

## Key Lessons Learned

1. **WebRTC is P2P**: Signaling server only for handshake, audio flows directly between browsers
2. **STUN/TURN are essential**: Public STUN for most cases, TURN server for strict firewalls
3. **NAT traversal is hard**: About 10-20% of connections need TURN relay
4. **Quality varies by network**: Implement adaptive bitrate and quality indicators
5. **Connection management is complex**: Proper cleanup prevents memory leaks
6. **CloudFront needs configuration**: Default settings don't work well with WebSocket

---

## Cost Analysis

**Infrastructure Choices:**
- **Signaling Server**: Express.js on AWS Lambda (cost-effective, scalable)
- **STUN Server**: Public servers (stun.l.google.com) - Free
- **TURN Server**: Would need dedicated EC2 instance for high traffic

**Total infrastructure cost for voice chat: ~$5-12/month** (without TURN)

---

## Conclusion

Building real-time voice communication with WebRTC was one of the most technically challenging and rewarding parts of my internship. The combination of browser APIs, WebSocket signaling, and AWS infrastructure created a powerful voice chat system comparable to Discord.

The key insight: **WebRTC is powerful but requires careful orchestration**. The browser handles the heavy lifting (audio encoding, network traversal), but you must implement robust signaling, connection management, and error recovery.

---

## Related Posts

- [Building Serverless Applications with AWS Lambda and DynamoDB](../3.1-blog1/)
- [Debugging Production Issues on CloudFront CDN](../3.3-blog3/)

---

*Written during my 12-week internship at FCJ (April 17 - July 10, 2026) as part of the AI Meeting Workforce Platform project.*
