---
title: "Bài viết Blog"
date: 2025-01-15
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

# Bài viết Blog

Trong 12 tuần thực tập tại FCJ, tôi đã ghi chép các bài học và kinh nghiệm quan trọng thông qua các bài viết blog. Những bài viết này chia sẻ thông tin chi tiết về kiến trúc AWS Serverless, thách thức giao tiếp thời gian thực và kinh nghiệm triển khai production.

---

## Blog 1: Xây dựng Ứng dụng Serverless với AWS Lambda và DynamoDB

{{< figure src="/NTL0210-FACJ_Worklog/images/blog1.png" title="Tổng quan Kiến trúc AWS Serverless" >}}

Trong 12 tuần thực tập tại FCJ, tôi đã xây dựng một ứng dụng serverless sẵn sàng cho production sử dụng AWS Lambda và DynamoDB. Bài viết này chia sẻ các quyết định kiến trúc, mẫu thiết kế và best practices mà tôi đã học được khi triển khai nền tảng AI Meeting Workforce Platform đa người thuê.

### Tại sao chọn Serverless?

Kiến trúc serverless cung cấp nhiều lợi ích hấp dẫn:
- **Không cần quản lý server**: Tập trung vào code, không phải infrastructure
- **Tự động mở rộng quy mô**: Xử lý lưu lượng tăng đột biến mà không cần can thiệp thủ công
- **Giá theo mức sử dụng**: Chỉ trả tiền cho thời gian tính toán thực tế
- **Độ khả dụng cao**: Tính dự phòng tích hợp sẵn trên nhiều availability zones

Đối với dự án thực tập với mẫu sử dụng khó dự đoán và ngân sách hạn chế, serverless là lựa chọn hoàn hảo.

### Thiết kế Single-Table cho DynamoDB

Một trong những quyết định kiến trúc quan trọng nhất là sử dụng **thiết kế single-table** cho DynamoDB. Thay vì tạo các bảng riêng biệt cho mỗi entity, mọi thứ đều nằm trong một bảng với cấu trúc này:

```
PK                    SK                      Attributes
---------------------------------------------------------
WORKSPACE#123         METADATA#               name, createdAt, ...
WORKSPACE#123         USER#alice              role, joinedAt, ...
WORKSPACE#123         MEETING#abc             title, date, ...
WORKSPACE#123         TASK#xyz                title, assignee, ...
```

**Lợi ích:**
- Hiệu suất tốt hơn: Truy xuất dữ liệu liên quan trong một query duy nhất
- Chi phí thấp hơn: Ít thao tác đọc/ghi hơn
- Đơn giản hóa access patterns: Tất cả dữ liệu được tổ chức theo workspace
- Dễ dàng multi-tenancy: Cách ly tự nhiên giữa các workspaces

### Thách thức Chính & Giải pháp

**Thách thức 1: Cold Starts**
- Vấn đề: Request đầu tiên sau thời gian idle mất 2-3 giây
- Giải pháp: Giảm kích thước package Lambda, sử dụng AWS SDK v3, triển khai connection pooling
- Kết quả: Cold starts giảm xuống < 1 giây

**Thách thức 2: Cách ly Dữ liệu Multi-Tenant**
- Vấn đề: Nguy cơ rò rỉ dữ liệu giữa các workspaces
- Giải pháp: Xác thực nghiêm ngặt trong mọi Lambda function, workspace ID trong mọi key DynamoDB
- Kết quả: Không có sự cố rò rỉ dữ liệu trên tất cả các workspaces

**Thách thức 3: Quản lý Chi phí**
- Vấn đề: Giá on-demand của DynamoDB khó dự đoán
- Giải pháp: Triển khai lớp caching, tối ưu hóa queries sử dụng GSIs
- Kết quả: Giữ chi phí hàng tháng dưới $15

### Tối ưu hóa Hiệu suất

**Tối ưu hóa Lambda:**
```javascript
// Trước: 2.5s cold start
import AWS from 'aws-sdk';

// Sau: <1s cold start
import { DynamoDBClient } from '@aws-sdk/client-dynamodb';
import { DynamoDBDocumentClient } from '@aws-sdk/lib-dynamodb';
```

**Tái sử dụng Connection:**
```javascript
// Global scope - được tái sử dụng qua các invocations
const dynamoClient = DynamoDBDocumentClient.from(
  new DynamoDBClient({ region: 'ap-southeast-1' })
);

export const handler = async (event) => {
  // Sử dụng connection hiện có
  const result = await dynamoClient.send(new GetCommand({...}));
};
```

### Bài học Rút ra

1. **Single-table design** rất mạnh mẽ nhưng cần lập kế hoạch cẩn thận
2. **AWS SDK v3** giảm đáng kể thời gian cold start
3. **Connection pooling** là điều cần thiết cho các Lambda function production
4. **Multi-tenancy** yêu cầu xác thực nghiêm ngặt ở mọi lớp
5. **Serverless** hoàn hảo cho workloads khó dự đoán với ngân sách hạn chế

Xây dựng nền tảng serverless này đã dạy tôi rằng AWS Lambda và DynamoDB không chỉ là buzzwords—chúng là các công cụ sẵn sàng cho production có thể xử lý ứng dụng thực tế khi được thiết kế đúng cách.

---

## Blog 2: Giao tiếp Voice Thời gian thực với WebRTC trên AWS

{{< figure src="/NTL0210-FACJ_Worklog/images/blog2.png" title="Kiến trúc Giao tiếp Voice WebRTC" >}}

Một trong những tính năng thách thức nhất mà tôi triển khai trong thời gian thực tập tại FCJ là giao tiếp voice thời gian thực sử dụng WebRTC. Bài viết này chia sẻ hành trình kỹ thuật xây dựng hệ thống voice chat giống Discord trên hạ tầng AWS.

### WebRTC là gì?

**WebRTC (Web Real-Time Communication)** là công nghệ trình duyệt cho phép:
- **Peer-to-peer audio/video** streaming
- **Độ trễ thấp**: <100ms cho hầu hết các kết nối
- **Không cần plugins**: Tích hợp sẵn trong các trình duyệt hiện đại
- **NAT traversal**: Hoạt động phía sau firewalls và routers

Hoàn hảo để xây dựng voice chat, video conferencing, hoặc tính năng chia sẻ màn hình.

### Tổng quan Kiến trúc

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
│ (Public ICE)  │                   │  (Relay nếu  │
│               │                   │  P2P fail)   │
└───────────────┘                   └──────────────┘

Sau khi signaling hoàn tất:
┌─────────────┐  Direct P2P Audio   ┌─────────────┐
│   Client A  │◄──────────────────►│   Client B  │
│  (Browser)  │   (Không tải server)│  (Browser)  │
└─────────────┘                     └─────────────┘
```

### Signaling Server

WebRTC yêu cầu **signaling server** để trao đổi thông tin kết nối (SDP offers/answers và ICE candidates). Tôi đã xây dựng server này sử dụng Express.js và Socket.io:

```javascript
// Signaling Server trên AWS Lambda
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

  // Tham gia voice channel
  socket.on('join-channel', ({ channelId, userId }) => {
    socket.join(channelId);
    
    // Thông báo cho người khác trong channel
    socket.to(channelId).emit('user-joined', { userId, socketId: socket.id });
  });

  // Chuyển tiếp WebRTC offer
  socket.on('webrtc-offer', ({ offer, to }) => {
    io.to(to).emit('webrtc-offer', {
      offer,
      from: socket.id
    });
  });

  // Chuyển tiếp WebRTC answer
  socket.on('webrtc-answer', ({ answer, to }) => {
    io.to(to).emit('webrtc-answer', {
      answer,
      from: socket.id
    });
  });

  // Chuyển tiếp ICE candidates
  socket.on('ice-candidate', ({ candidate, to }) => {
    io.to(to).emit('ice-candidate', {
      candidate,
      from: socket.id
    });
  });

  // Xử lý disconnect
  socket.on('disconnect', () => {
    console.log('User disconnected:', socket.id);
  });
});
```

### Triển khai WebRTC phía Client

**Bước 1: Tạo Peer Connection**
```javascript
const configuration = {
  iceServers: [
    { urls: 'stun:stun.l.google.com:19302' }, // STUN server miễn phí
    { urls: 'stun:stun1.l.google.com:19302' }
  ]
};

const peerConnection = new RTCPeerConnection(configuration);

// Lấy local audio stream
const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
stream.getTracks().forEach(track => {
  peerConnection.addTrack(track, stream);
});
```

**Bước 2: Xử lý ICE Candidates**
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

**Bước 3: Tạo Offer (Caller)**
```javascript
const offer = await peerConnection.createOffer();
await peerConnection.setLocalDescription(offer);

socket.emit('webrtc-offer', {
  offer: peerConnection.localDescription,
  to: remotePeerId
});
```

**Bước 4: Xử lý Answer (Receiver)**
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

**Bước 5: Phát Remote Audio**
```javascript
peerConnection.ontrack = (event) => {
  const audioElement = document.createElement('audio');
  audioElement.srcObject = event.streams[0];
  audioElement.autoplay = true;
  document.body.appendChild(audioElement);
};
```

### Thách thức & Giải pháp

**Thách thức 1: Thất bại NAT Traversal**
- Vấn đề: Một số người dùng phía sau firewalls nghiêm ngặt không thể kết nối
- Nguyên nhân gốc: Kết nối P2P trực tiếp thất bại do symmetric NAT
- Giải pháp: Triển khai TURN server để relay fallback
- Kết quả: Tỷ lệ kết nối thành công 99%

**Thách thức 2: Vấn đề WebSocket trên CloudFront**
- Vấn đề: Kết nối WebSocket không ổn định qua CloudFront CDN
- Nguyên nhân gốc: Cài đặt timeout mặc định của CloudFront
- Giải pháp: 
  - Cấu hình WebSocket timeout lên 3600 giây
  - Triển khai cơ chế heartbeat ping/pong
  - Thêm logic tự động reconnection
- Kết quả: Kết nối ổn định với khôi phục tự động

**Thách thức 3: Vấn đề Chất lượng Audio**
- Vấn đề: Người dùng báo cáo audio bị gián đoạn hoặc echo
- Nguyên nhân gốc: Biến động băng thông mạng và echo phần cứng
- Giải pháp:
  - Bật codec Opus với adaptive bitrate
  - Triển khai echo cancellation trong trình duyệt
  - Thêm chỉ báo chất lượng mạng
- Kết quả: Chất lượng audio rõ ràng trên hầu hết kết nối

**Thách thức 4: Quản lý Multiple Peer**
- Vấn đề: Quản lý kết nối WebRTC với 5+ người dùng trong một channel
- Nguyên nhân gốc: Mỗi người dùng cần peer connection với mọi người khác (độ phức tạp N²)
- Giải pháp: 
  - Tạo class PeerManager để xử lý lifecycle connection
  - Triển khai cleanup tự động khi disconnect
  - Thêm giám sát trạng thái kết nối
- Kết quả: Trải nghiệm mượt mà với tối đa 10 người dùng đồng thời

### Bài học Rút ra

1. **WebRTC là P2P**: Signaling server chỉ cho handshake, audio truyền trực tiếp giữa các trình duyệt
2. **STUN/TURN là cần thiết**: STUN công khai cho hầu hết trường hợp, TURN server cho firewalls nghiêm ngặt
3. **NAT traversal khó**: Khoảng 10-20% kết nối cần TURN relay
4. **Chất lượng thay đổi theo mạng**: Triển khai adaptive bitrate và chỉ báo chất lượng
5. **Quản lý connection phức tạp**: Cleanup đúng cách ngăn memory leaks
6. **CloudFront cần cấu hình**: Cài đặt mặc định không hoạt động tốt với WebSocket

### Chi phí Infrastructure

**Lựa chọn Infrastructure:**
- **Signaling Server**: Express.js trên AWS Lambda (tiết kiệm chi phí, có thể mở rộng)
- **STUN Server**: Public servers (stun.l.google.com) - Miễn phí
- **TURN Server**: Cần EC2 instance riêng cho traffic cao ($$)

**Phân tích Chi phí:**
- **Lambda**: ~$2-5/tháng cho signaling server
- **API Gateway WebSocket**: ~$1-2/tháng
- **CloudFront**: ~$2-5/tháng cho static assets
- **TURN Server**: Chưa triển khai (sẽ là ~$20-50/tháng cho EC2)

Tổng chi phí infrastructure cho voice chat: **~$5-12/tháng** (không có TURN)

### Kết luận

Xây dựng giao tiếp voice thời gian thực với WebRTC là một trong những phần thách thức nhất về mặt kỹ thuật và đáng giá nhất trong thời gian thực tập của tôi. Sự kết hợp giữa browser APIs, WebSocket signaling, và hạ tầng AWS đã tạo ra một hệ thống voice chat mạnh mẽ có thể so sánh với Discord.

Insight chính: **WebRTC rất mạnh mẽ nhưng cần orchestration cẩn thận**. Trình duyệt xử lý phần nặng nhọc (audio encoding, network traversal), nhưng bạn phải triển khai signaling, quản lý connection và khôi phục lỗi mạnh mẽ.

Đối với bất kỳ ai xây dựng tính năng tương tự, hãy bắt đầu đơn giản (kết nối 2 peer), kiểm tra kỹ lưỡng trên các mạng khác nhau, và luôn có cơ chế fallback khi P2P thất bại.

---

## Blog 3: Gỡ lỗi Vấn đề Production trên CloudFront CDN

{{< figure src="/NTL0210-FACJ_Worklog/images/blog3.png" title="Quy trình Gỡ lỗi CloudFront" >}}

Tuần 11 trong thời gian thực tập tại FCJ của tôi rất căng thẳng—sau khi triển khai ứng dụng serverless lên CloudFront, nhiều vấn đề production xuất hiện. Bài viết này ghi lại hành trình gỡ lỗi và bài học rút ra từ triển khai production.

### Kịch bản Triển khai

Sau 10 tuần phát triển local, đã đến lúc triển khai lên production:
- **Frontend**: Next.js static site trên CloudFront + S3
- **Backend**: Express.js Lambda functions đằng sau API Gateway
- **Real-time**: WebRTC signaling server trên Lambda riêng
- **Authentication**: AWS Cognito với JWT tokens

Mọi thứ hoạt động hoàn hảo trong phát triển local (`localhost:3000`). Sau đó tôi triển khai lên CloudFront...

### Chuỗi Vấn đề

**Bốn Lỗi Production Nghiêm trọng:**
1. ❌ **Login thất bại** - Người dùng không thể xác thực
2. ❌ **Voice chat thất bại** - Kết nối WebRTC không thiết lập được
3. ❌ **Text chat không ổn định** - Tin nhắn bị mất hoặc trễ
4. ❌ **Workspace invitations thất bại** - Email mời không gửi được

Hãy gỡ lỗi từng cái một.

---

### Vấn đề 1: Lỗi Xác thực Login

**Triệu chứng:**
```
POST /auth/login → 200 OK
Nhưng user vẫn thấy "Unauthorized" trên protected routes
Browser console: "No valid auth token found"
```

**Giả thuyết Ban đầu:** JWT token không được lưu đúng cách.

**Điều tra:**
```javascript
// Kiểm tra browser DevTools → Application → Cookies
// Kết quả: Cookie tồn tại trên localhost nhưng KHÔNG trên CloudFront domain
```

**Phân tích Nguyên nhân Gốc:**

1. **Backend đã đặt cookie với domain=localhost**
```javascript
// Hoạt động trong development nhưng thất bại trong production
res.cookie('auth_token', token, {
  httpOnly: true,
  secure: true,
  domain: 'localhost' // ❌ Sai cho production!
});
```

2. **CloudFront domain không khớp:**
   - Production URL: `https://d1234abcd.cloudfront.net`
   - Cookie domain: `localhost`
   - Browser: "Chúng không khớp, từ chối cookie"

3. **CORS headers chưa đầy đủ:**
```javascript
// Backend cho phép origin nhưng không cho phép credentials
app.use(cors({
  origin: 'https://d1234abcd.cloudfront.net',
  credentials: false // ❌ Nên là true!
}));
```

**Giải pháp:**

```javascript
// backend/auth.js
const DOMAIN = process.env.NODE_ENV === 'production' 
  ? '.cloudfront.net'  // Khớp với CloudFront domain
  : 'localhost';

res.cookie('auth_token', token, {
  httpOnly: true,
  secure: true,
  sameSite: 'none', // Bắt buộc cho cross-origin
  domain: DOMAIN,
  maxAge: 7 * 24 * 60 * 60 * 1000 // 7 ngày
});

// backend/server.js
app.use(cors({
  origin: process.env.FRONTEND_URL,
  credentials: true // ✅ Cho phép credentials
}));
```

**Cấu hình CloudFront:**
```yaml
# Allowed headers trong CloudFront behavior
AllowedHeaders:
  - Authorization
  - Cookie
  - Content-Type
AllowCredentials: true
```

**Kết quả:** ✅ Login hoạt động, cookies tồn tại qua các requests

---

### Vấn đề 2: Lỗi WebRTC Voice Chat

**Triệu chứng:**
```
User clicks "Join Voice Channel"
Loading spinner xuất hiện... mãi mãi
Browser console: "WebSocket connection failed"
```

**Điều tra:**

1. **Kiểm tra signaling server logs:**
```bash
CloudWatch Logs → /aws/lambda/signaling-server
Error: "Connection timeout after 30 seconds"
```

2. **Test kết nối WebSocket thủ công:**
```javascript
const ws = new WebSocket('wss://api.example.com/signaling');
ws.onerror = (err) => console.log(err);
// Error: "WebSocket is closed before the connection is established"
```

**Phân tích Nguyên nhân Gốc:**

1. **API Gateway WebSocket timeout:**
   - Default timeout: 10 phút
   - CloudFront timeout: 30 giây (ghi đè!)
   - WebSocket cần kết nối dài hạn

2. **Thiếu WebSocket upgrade headers:**
```
CloudFront không chuyển tiếp:
- Connection: Upgrade
- Upgrade: websocket
```

3. **Origin sai cho STUN/TURN:**
```javascript
// Frontend đang sử dụng localhost STUN config trong production
const iceServers = [
  { urls: 'stun:localhost:3478' } // ❌ Sai!
];
```

**Giải pháp:**

**1. Cấu hình CloudFront WebSocket:**
```yaml
# CloudFront behavior cho /signaling path
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

**2. Cài đặt API Gateway:**
```javascript
// Mở rộng timeout cho WebSocket connections
const apiGatewayConfig = {
  timeout: 3600000, // 1 giờ tính bằng milliseconds
  keepAlive: true
};
```

**3. Cấu hình STUN/TURN Frontend:**
```javascript
// Sử dụng public STUN servers trong production
const iceServers = process.env.NODE_ENV === 'production'
  ? [
      { urls: 'stun:stun.l.google.com:19302' },
      { urls: 'stun:stun1.l.google.com:19302' }
    ]
  : [{ urls: 'stun:localhost:3478' }];
```

**4. Triển khai Heartbeat Ping/Pong:**
```javascript
// Giữ WebSocket sống qua CloudFront
socket.on('connect', () => {
  setInterval(() => {
    socket.emit('ping');
  }, 25000); // Ping mỗi 25 giây (trước 30s timeout)
});

// Server phản hồi để giữ kết nối sống
socket.on('ping', () => {
  socket.emit('pong');
});
```

**Kết quả:** ✅ Voice chat kết nối ổn định, WebSocket luôn sống

---

### Vấn đề 3: Text Chat Không Ổn định

**Triệu chứng:**
```
Tin nhắn gửi thành công đôi khi
Lúc khác: "Message failed to send"
Ngắt kết nối ngẫu nhiên mỗi 2-3 phút
```

**Điều tra:**

```javascript
// Browser DevTools → Network → WS
// Kết nối WebSocket đóng bất ngờ
// Close code: 1006 (Abnormal Closure)
```

**Nguyên nhân Gốc:**

1. **Không có logic tự động reconnection**
2. **CloudFront đóng kết nối WebSocket idle**
3. **Không có queuing cho tin nhắn thất bại**

**Giải pháp:**

```javascript
// Triển khai tự động reconnection với exponential backoff
class ReconnectingWebSocket {
  constructor(url) {
    this.url = url;
    this.reconnectAttempts = 0;
    this.maxReconnectAttempts = 5;
    this.reconnectDelay = 1000; // Bắt đầu với 1 giây
    this.messageQueue = [];
    
    this.connect();
  }

  connect() {
    this.socket = io(this.url, {
      transports: ['websocket', 'polling'],
      reconnection: true,
      reconnectionAttempts: this.maxReconnectAttempts,
      reconnectionDelay: this.reconnectDelay,
      reconnectionDelayMax: 10000 // Tối đa 10 giây
    });

    this.socket.on('connect', () => {
      console.log('✅ Đã kết nối với chat server');
      this.reconnectAttempts = 0;
      this.reconnectDelay = 1000;
      
      // Gửi lại tin nhắn trong queue
      this.flushMessageQueue();
    });

    this.socket.on('disconnect', (reason) => {
      console.log('⚠️ Ngắt kết nối:', reason);
      
      if (reason === 'io server disconnect') {
        // Server đá chúng ta, reconnect thủ công
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
      
      console.log(`Đang reconnect trong ${delay}ms...`);
      
      setTimeout(() => this.connect(), delay);
    }
  }

  send(message) {
    if (this.socket.connected) {
      this.socket.emit('message', message);
    } else {
      // Queue tin nhắn nếu ngắt kết nối
      this.messageQueue.push(message);
    }
  }

  flushMessageQueue() {
    while (this.messageQueue.length > 0) {
      const message = this.messageQueue.shift();
      this.socket.emit('message', message);
    }
  }
}
```

**Kết quả:** ✅ Tin nhắn gửi đi đáng tin cậy, tự động reconnection hoạt động

---

### Vấn đề 4: Lỗi Gửi Email Workspace Invitation

**Triệu chứng:**
```
User gửi workspace invitation
Frontend hiển thị "Invitation sent" ✅
Nhưng người nhận không bao giờ nhận được email 📭
```

**Điều tra:**

```bash
# Kiểm tra Lambda logs
CloudWatch → /aws/lambda/send-invitation
Error: "Missing AWS credentials"

# Kiểm tra IAM role permissions
Lambda execution role không có quyền SES:SendEmail ❌
```

**Nguyên nhân Gốc:**

Lambda function không thể gửi email qua AWS SES do thiếu IAM permissions.

**Giải pháp:**

```json
// IAM policy cho Lambda execution role
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
      Subject: { Data: `Bạn được mời vào ${workspaceName}` },
      Body: {
        Html: {
          Data: `
            <h2>Tham gia ${workspaceName} trên AI Meeting Platform</h2>
            <p>Nhấn vào liên kết bên dưới để chấp nhận lời mời:</p>
            <a href="${inviteLink}">Chấp nhận Lời mời</a>
          `
        }
      }
    }
  };

  try {
    await sesClient.send(new SendEmailCommand(params));
    console.log('✅ Email mời đã gửi tới', email);
  } catch (error) {
    console.error('❌ Thất bại khi gửi email:', error);
    throw error;
  }
};
```

**Kết quả:** ✅ Email mời được gửi thành công

---

### Bài học Rút ra

**1. Local Development ≠ Production**
- Luôn test trong môi trường staging phản ánh production
- CloudFront có hành vi khác với localhost
- CORS, cookies, và WebSockets hoạt động khác nhau

**2. Cấu hình Cookie rất Quan trọng**
- Cài đặt `domain`, `sameSite`, `secure` là then chốt
- Production cần `sameSite: 'none'` cho cross-origin cookies
- Luôn đặt `credentials: true` trong CORS nếu sử dụng cookies

**3. WebSocket qua CDN Khó**
- CloudFront có idle timeout mặc định 30 giây
- Triển khai heartbeat ping/pong để giữ kết nối sống
- Chuyển tiếp WebSocket upgrade headers một cách rõ ràng
- Cấu hình cache behavior riêng cho WebSocket paths

**4. Luôn Triển khai Logic Reconnection**
- Mạng không đáng tin cậy, lên kế hoạch cho disconnections
- Exponential backoff ngăn server quá tải
- Queue tin nhắn trong quá trình reconnection
- Hiển thị trạng thái kết nối cho người dùng

**5. IAM Permissions Không Tùy chọn**
- Mỗi dịch vụ AWS cần permissions rõ ràng
- Test tất cả integrations trong môi trường giống production
- Giám sát CloudWatch logs cho permission errors

**6. Giám sát Mọi thứ trong Production**
- CloudWatch logs đã tiết kiệm thời gian gỡ lỗi
- Giám sát thời gian thực giúp phát hiện vấn đề sớm
- Thiết lập alarms cho các lỗi nghiêm trọng

### Bộ công cụ Gỡ lỗi

**Công cụ Cần thiết Đã sử dụng:**
1. **Browser DevTools**
   - Tab Network cho API calls
   - Tab Application cho cookies
   - Console cho errors

2. **AWS CloudWatch**
   - Lambda logs cho backend errors
   - API Gateway logs cho requests
   - Custom metrics cho monitoring

3. **Postman / cURL**
   - Test API endpoints trực tiếp
   - Xác minh headers và responses

4. **WebSocket Test Clients**
   - Test kết nối WebSocket độc lập
   - Xác minh hành vi signaling server

### Kết luận

Triển khai lên CloudFront đã dạy tôi rằng **production là một thế giới khác**. Các vấn đề mà tôi gặp phải—authentication cookies, kết nối WebRTC, tính ổn định WebSocket, và quyền email—tất cả đều vô hình trong phát triển local nhưng quan trọng trong production.

Bài học quan trọng nhất: **Luôn có môi trường staging** phản ánh production. Test trên CloudFront staging trước sẽ đã phát hiện những vấn đề này trước khi chúng ảnh hưởng người dùng.

Tuần 11 rất khó khăn, nhưng gỡ lỗi các vấn đề production này đã cho tôi kinh nghiệm vô giá về:
- Phương pháp troubleshooting production
- Hành vi và cấu hình CDN
- Hạ tầng giao tiếp thời gian thực
- Tích hợp dịch vụ AWS trong production

Những bài học này sẽ ở lại với tôi trong suốt sự nghiệp của tôi trong cloud engineering.

---

## Kết luận

Ba bài viết blog này ghi lại các thách thức kỹ thuật chính từ 12 tuần thực tập FCJ của tôi:

1. **Blog 1**: Kiến trúc serverless với AWS Lambda và thiết kế single-table DynamoDB
2. **Blog 2**: Giao tiếp voice thời gian thực sử dụng WebRTC, signaling servers, và NAT traversal
3. **Blog 3**: Gỡ lỗi production trên CloudFront CDN, sửa cookies, WebSockets, và permissions

Mỗi thách thức đã dạy tôi rằng xây dựng ứng dụng sẵn sàng cho production đòi hỏi nhiều hơn việc chỉ viết code—nó đòi hỏi hiểu biết về infrastructure, phương pháp gỡ lỗi, và kỹ thuật resilience.

**Bài học Chính:**
- Kiến trúc serverless mạnh mẽ cho khả năng mở rộng tiết kiệm chi phí
- Giao tiếp thời gian thực cần orchestration cẩn thận và cơ chế fallback
- Triển khai production tiết lộ các vấn đề vô hình trong development
- AWS cung cấp các công cụ tuyệt vời, nhưng mỗi cái cần cấu hình đúng
- Logging và monitoring toàn diện là cần thiết cho việc gỡ lỗi

Cảm ơn bạn đã đọc! Những kinh nghiệm này đã hình thành hiểu biết của tôi về kiến trúc cloud hiện đại và chuẩn bị cho tôi xây dựng các hệ thống production ở quy mô lớn.

---

*Những bài viết blog này được viết trong 12 tuần thực tập tại FCJ (17/4 - 10/7/2026) như một phần của dự án AI Meeting Workforce Platform. Để biết thêm chi tiết, xem [worklog đầy đủ](/) và [đề xuất dự án](/2-proposal/).*
