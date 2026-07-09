---
title: "Đề xuất dự án"
date: 2026-04-17
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# NỀN TẢNG QUẢN LÝ CÔNG VIỆC TỪ CUỘC HỌP AI
## Giải pháp AWS Serverless để Chuyển đổi Cuộc họp thành Thực thi Đo lường được

---

### 1. Tóm tắt điều hành

Nền tảng Quản lý Công việc từ Cuộc họp AI là một giải pháp toàn diện được thiết kế để chuyển đổi các cuộc thảo luận trong cuộc họp thành kết quả thực thi đo lường được. Được xây dựng trong 12 tuần thực tập tại FCJ (tháng 4 - tháng 7 năm 2026), nền tảng này thể hiện việc triển khai kiến trúc AWS Serverless hiện đại với giao tiếp voice theo thời gian thực, phân tích cuộc họp được hỗ trợ bởi AI và các tính năng cộng tác workspace.

**Thời gian dự án:** 17/04/2026 - 10/07/2026 (12 tuần)

**Công nghệ sử dụng:**
- **Frontend:** Next.js 16, React 19, TailwindCSS
- **Backend:** AWS Lambda, API Gateway, Express.js
- **Cơ sở dữ liệu:** Amazon DynamoDB với thiết kế single-table
- **Lưu trữ:** Amazon S3 với lifecycle policies
- **Xác thực:** AWS Cognito
- **Xử lý AI:** AWS Transcribe, Google Gemini/OpenAI
- **Thời gian thực:** WebRTC, Socket.io, Custom signaling server
- **Triển khai:** AWS Amplify, CloudFront

**Tính năng chính đã triển khai:**
- Quản lý workspace đa người thuê với số lượng team không giới hạn
- Giao tiếp voice theo thời gian thực (trải nghiệm giống Discord)
- Chat văn bản với lịch sử tin nhắn
- Ghi âm và phiên âm cuộc họp
- Phân tích cuộc họp và trích xuất nhiệm vụ bằng AI
- Hệ thống mời thành viên workspace
- Kiểm soát truy cập dựa trên vai trò

---

### 2. Mô tả vấn đề

#### Vấn đề là gì?

Các nhóm hiện đại tiến hành vô số cuộc họp nhưng gặp khó khăn với việc thực thi:
- **Mất các hành động cần làm**: Các quyết định cuộc họp phân tán qua email, chat và ghi chú
- **Không theo dõi**: Thành viên nhóm quên các cam kết được thực hiện trong cuộc thảo luận
- **Lãng phí thời gian**: Hàng giờ đồng hồ được dành để tóm tắt cuộc họp và tạo nhiệm vụ thủ công
- **Không có khả năng hiển thị**: Không có cách tập trung để theo dõi các quyết định và trách nhiệm
- **Khoảng trống trách nhiệm**: Khó khăn trong việc theo dõi tiến độ các cam kết cuộc họp

#### Giải pháp

Nền tảng sử dụng **Kiến trúc AWS Serverless** để tự động hóa quy trình từ cuộc họp đến thực thi:

1. **Ghi âm & Phiên âm Cuộc họp**: Ghi âm dựa trên trình duyệt với AWS Transcribe
2. **Phân tích AI**: Google Gemini/OpenAI xử lý bản ghi để tạo tóm tắt
3. **Tạo nhiệm vụ tự động**: Các mục hành động được AI xác định trở thành nhiệm vụ có thể theo dõi
4. **Voice theo thời gian thực**: Giao tiếp voice dựa trên WebRTC để cộng tác liền mạch
5. **Quản lý Workspace**: Kiến trúc đa người thuê với quản lý team và kênh
6. **Theo dõi thực thi**: Bảng điều khiển theo thời gian thực hiển thị tiến độ và hoàn thành nhiệm vụ

**Điểm khác biệt chính:**
- Tạo và phân công nhiệm vụ tự động
- Workspace tích hợp cho cộng tác nhóm
- Giao tiếp voice theo thời gian thực được tích hợp sẵn
- Tập trung vào đo lường kết quả thực thi

---

### 3. Kiến trúc giải pháp

Nền tảng sử dụng Kiến trúc AWS Serverless với hỗ trợ đa người thuê và cô lập workspace.

**Kiến trúc cấp cao:**

![Kiến trúc cấp cao](/NTL0210-FACJ_Worklog/images/arch1.png)

#### Dịch vụ AWS được sử dụng

• **AWS Lambda**: Tính toán serverless cho logic API và xử lý AI  
• **Amazon DynamoDB**: Cơ sở dữ liệu NoSQL với thanh toán theo yêu cầu  
• **Amazon S3**: Lưu trữ đối tượng cho bản ghi âm và bản ghi  
• **AWS Step Functions**: Điều phối pipeline xử lý AI  
• **Amazon API Gateway**: Điểm cuối REST API  
• **AWS Cognito**: Xác thực người dùng với JWT tokens  
• **AWS Amplify & CloudFront**: Lưu trữ trang tĩnh và CDN  
• **AWS Transcribe**: Chuyển đổi giọng nói sang văn bản tự động  
• **Amazon CloudWatch**: Ghi log và giám sát  
• **AWS IAM**: Kiểm soát truy cập chi tiết

---

### 4. Triển khai kỹ thuật

#### Tiến trình triển khai (Tiến độ thực tế)

**Tuần 1-2 (17-26/04): Onboarding & Kiến thức AWS cơ bản**
- Hoàn thành quy trình onboarding AWS Study Group
- Học Module 1 AWS (Cloud Concepts) và Module 2 (Security & Compliance)
- Thiết lập tài khoản AWS và cấu hình IAM
- Học các mẫu kiến trúc đám mây cơ bản

**Tuần 3-4 (27/04 - 10/05): Dịch vụ AWS cốt lõi**
- Hoàn thành Module 3 (Technology & Services) và labs Module 4
- Thực hành với EC2, VPC, S3 và RDS
- Tham dự AWS Study Group Workshop (09/05) - AI trong Phát triển
- Gặp phải và gỡ lỗi lab (trải nghiệm học tập)

**Tuần 5-6 (11-24/05): AWS nâng cao & Tham gia Cộng đồng**
- Hoàn thành Module 5 và bắt đầu Module 6
- Làm việc trên các dự án AWS cá nhân áp dụng các khái niệm đã học
- Tham dự FCAJ Community Day (23/05) với 6 chuyên gia
- Học về tầm quan trọng của AI context và các tính năng nâng cao của CloudFront

**Tuần 7 (25/05 - 07/06): Lập kế hoạch Dự án & Module 7**
- Hoàn thành Module 7 (khái niệm AWS nâng cao)
- Tham gia công việc dự án nhóm (3 ngày)
- Brainstorm và lập kế hoạch kiến trúc dự án cuối
- Tạo đề xuất dự án ban đầu

**Tuần 8 (08-14/06): Thiết kế Kiến trúc & Bắt đầu Phát triển**
- Bắt đầu sửa đổi thiết kế kiến trúc
- Bắt đầu triển khai các tính năng dự án cốt lõi
- Thiết lập môi trường phát triển
- Khởi động lập kế hoạch tích hợp dịch vụ AWS

**Tuần 9 (15-21/06): Thách thức Tích hợp AWS**
- Tiếp tục phát triển dự án
- Thử tích hợp dịch vụ AWS
- Gặp phải thách thức kết nối (trải nghiệm học tập)
- Tham dự AWS Certification Quiz Competition (20/06)
- Học về resource-based vs identity-based policies

**Tuần 10 (22-28/06): Triển khai Tính năng**
- Phát triển các tính năng dự án cốt lõi:
  - Hệ thống xác thực người dùng
  - Tính năng giao tiếp thời gian thực
  - Cải thiện UI/UX
  - Tích hợp API backend
- Tham dự FCAJ Community Day (27/06) - AI Agents & DevOps
- Học từ 9 diễn giả về AgenticOps, Voice AI, AWS DevOps Agent, Amazon Q và MCP

**Tuần 11 (29/06 - 05/07): Triển khai CloudFront & Gỡ lỗi**
- Triển khai ứng dụng lên CloudFront để kiểm tra
- Gặp phải và sửa nhiều vấn đề triển khai:
  - **Lỗi đăng nhập**: Cấu hình CORS, vấn đề domain cookie
  - **Lỗi voice chat**: WebRTC signaling, cấu hình STUN/TURN
  - **Lỗi text chat**: Sự ổn định kết nối WebSocket
  - **Lỗi mời workspace**: Vấn đề tích hợp dịch vụ email
- Học gỡ lỗi production và phân tích nguyên nhân gốc

**Tuần 12 (06-10/07): Kiểm tra Cuối cùng & Tài liệu**
- Kiểm tra và xác thực toàn diện
- Hoàn thiện triển khai production
- Hoàn thành tài liệu
- Tổng kết thực tập và thuyết trình

#### Thách thức Kỹ thuật Chính & Giải pháp

**Thách thức 1: Tích hợp Dịch vụ AWS**
- **Vấn đề**: Nỗ lực ban đầu kết nối dịch vụ AWS gặp vấn đề kết nối
- **Giải pháp**: Nghiên cứu best practices AWS, cấu hình VPC endpoints, triển khai chính sách IAM phù hợp
- **Học tập**: Hiểu về mô hình mạng và bảo mật AWS

**Thách thức 2: Lỗi Triển khai CloudFront**
- **Vấn đề**: Nhiều lỗi khi triển khai lên CloudFront (login, voice, chat, invitations)
- **Nguyên nhân gốc**: 
  - Headers CORS không được cấu hình cho CDN
  - Flags bảo mật cookie không tương thích với CloudFront
  - Khả năng truy cập WebRTC signaling server qua CDN
  - Sự không ổn định kết nối WebSocket
- **Giải pháp**:
  - Cập nhật cấu hình CORS cho CloudFront distribution
  - Cấu hình cài đặt cookie phù hợp (SameSite, Secure, Domain)
  - Cấu hình lại WebRTC signaling và STUN/TURN servers
  - Triển khai logic reconnection WebSocket
- **Học tập**: Gỡ lỗi môi trường production, kiến trúc CDN, thách thức giao tiếp thời gian thực

**Thách thức 3: Giao tiếp Voice Thời gian thực**
- **Vấn đề**: Triển khai WebRTC cho voice chat với độ trễ thấp
- **Giải pháp**: Xây dựng signaling server tùy chỉnh, triển khai kết nối peer-to-peer, tối ưu hóa audio codecs
- **Học tập**: Kiến trúc WebRTC, NAT traversal, media streaming

#### Công nghệ sử dụng

**Frontend:**
- Next.js 16.2.9 với React 19
- TailwindCSS cho thiết kế responsive
- Socket.io Client cho tính năng thời gian thực
- WebRTC cho giao tiếp voice
- AWS Amplify SDK cho xác thực

**Backend:**
- Express.js trên AWS Lambda
- TypeScript với strict mode
- AWS SDK v3 (DynamoDB, S3, Cognito)
- WebRTC signaling server tùy chỉnh
- Step Functions cho AI pipeline

**Cơ sở dữ liệu & Lưu trữ:**
- Thiết kế single-table DynamoDB với GSIs
- S3 cho các tệp âm thanh và bản ghi
- Thanh toán theo yêu cầu để tối ưu hóa chi phí

**Xử lý AI:**
- AWS Transcribe cho speech-to-text
- Google Gemini / OpenAI cho phân tích
- Lambda functions cho xử lý
- Step Functions cho điều phối

**Triển khai:**
- AWS Amplify cho lưu trữ frontend
- CloudFront CDN cho phân phối toàn cầu
- Lambda cho backend serverless
- GitHub Actions cho CI/CD

---

### 5. Tiến trình & Các mốc quan trọng

#### Tiến trình Dự án Thực tế

| Tuần | Ngày | Khu vực Tập trung | Thành tựu Chính |
|------|------|-------------------|------------------|
| 1-2 | 17-26/04 | Onboarding | Cơ bản AWS, Module 1-2, thiết lập tài khoản |
| 3-4 | 27/04 - 10/05 | Dịch vụ Cốt lõi | Module 3-4, labs EC2/VPC/S3, AWS Workshop |
| 5-6 | 11-24/05 | Khái niệm Nâng cao | Module 5-6, dự án cá nhân, FCAJ Community Day |
| 7 | 25/05 - 07/06 | Lập kế hoạch Dự án | Module 7, dự án nhóm, tạo đề xuất |
| 8 | 08-14/06 | Kiến trúc | Sửa đổi thiết kế, bắt đầu phát triển |
| 9 | 15-21/06 | Tích hợp | Nỗ lực tích hợp AWS, Quiz Competition |
| 10 | 22-28/06 | Triển khai | Tính năng Auth, voice, UI, FCAJ Day (AI/DevOps) |
| 11 | 29/06 - 05/07 | Triển khai | Triển khai CloudFront, sửa lỗi |
| 12 | 06-10/07 | Hoàn thiện | Kiểm tra, tài liệu, thuyết trình |

#### Đạt được các Mốc quan trọng

**✅ Mốc Tháng 1 (17/04 - 17/05):**
- Hoàn thành AWS Modules 1-4 với labs thực hành
- Tham dự AWS Study Group Workshop
- Thiết lập môi trường phát triển AWS
- Đạt được kiến thức cloud cơ bản

**✅ Mốc Tháng 2 (18/05 - 17/06):**
- Hoàn thành AWS Modules 5-7
- Tham dự FCAJ Community Day (23/05)
- Hoàn thành công việc dự án nhóm
- Bắt đầu phát triển dự án cuối
- Tham dự AWS Certification Quiz (20/06)

**✅ Mốc Tháng 3 (18/06 - 10/07):**
- Tham dự FCAJ Community Day (27/06) - AI/DevOps
- Triển khai tính năng cốt lõi (auth, voice, chat, workspace)
- Triển khai lên CloudFront
- Sửa vấn đề triển khai production
- Hoàn thành kiểm tra toàn diện
- Hoàn thiện tài liệu

---

### 6. Ước tính Ngân sách

#### Chi phí Hạ tầng (Thực tế Hàng tháng - Giai đoạn Phát triển)

**Tính toán:**
- **AWS Lambda**: $2-5/tháng
  - Ước tính 50,000-100,000 requests/tháng
  - Bộ nhớ trung bình 512 MB
  - Free tier bao phủ phần lớn sử dụng

**Lưu trữ:**
- **Amazon S3**: $3-8/tháng
  - Standard storage cho audio/transcripts
  - Lifecycle policy sang tiers rẻ hơn
  - ~5,000 requests/tháng

- **Amazon DynamoDB**: $5-12/tháng
  - On-demand billing mode
  - Ước tính 30,000 read/write units
  - 3-5 GB storage

**Xử lý AI:**
- **AWS Transcribe**: $8-15/tháng
  - Sử dụng hạn chế trong kiểm tra
  - $0.024 mỗi phút cho standard

- **LLM API (Gemini/OpenAI)**: $5-10/tháng
  - Rate-limited để kiểm soát chi phí
  - Cached results trong DynamoDB

**Mạng:**
- **CloudFront**: $2-5/tháng
  - Phân phối static asset
  - Chi phí truyền dữ liệu

- **API Gateway**: $1-2/tháng
  - REST API requests
  - Trong free tier ban đầu

**Dịch vụ khác:**
- **AWS Amplify**: $0-1/tháng (free tier)
- **AWS Cognito**: $0/tháng (< 50,000 users free)
- **CloudWatch Logs**: $1-3/tháng (tối ưu hóa)

**Tổng Chi phí Hàng tháng: $27-61 USD**  
**Trung bình: ~$44/tháng trong phát triển**  
**Tổng Chi phí 3 Tháng: ~$132 USD**

#### Chiến lược Tối ưu hóa Chi phí Đã triển khai

1. **On-Demand Pricing**: Sử dụng DynamoDB on-demand mode cho workloads không thể dự đoán
2. **Tối ưu hóa Lambda**: Tối ưu hóa cài đặt memory và timeout của function
3. **S3 Lifecycle Policies**: Tự động chuyển sang tiers lưu trữ rẻ hơn
4. **CloudFront Caching**: Giảm requests origin thông qua caching phù hợp
5. **Sử dụng Free Tier**: Tối đa hóa lợi ích AWS free tier trong phát triển
6. **Resource Tagging**: Theo dõi chi phí theo tính năng và môi trường

---

### 7. Đánh giá Rủi ro & Giảm thiểu

#### Rủi ro Kỹ thuật

**Rủi ro 1: Vấn đề Triển khai CloudFront (CAO - Đã xảy ra)**
- **Tác động**: Lỗi triển khai production ảnh hưởng đến tất cả người dùng
- **Khả năng**: CAO (Điều này thực sự đã xảy ra ở Tuần 11)
- **Giảm thiểu Đã triển khai**:
  - Kiểm tra kỹ lưỡng trong môi trường staging trước production
  - Ghi chép tất cả cấu hình cụ thể của CloudFront
  - Tạo quy trình rollback
  - Triển khai ghi log lỗi toàn diện
- **Bài học**: Kiểm tra local không đủ cho triển khai CDN

**Rủi ro 2: Lỗi Giao tiếp Thời gian thực (TRUNG BÌNH)**
- **Tác động**: Voice chat và text messaging không hoạt động đáng tin cậy
- **Khả năng**: TRUNG BÌNH
- **Giảm thiểu**:
  - Triển khai cơ chế fallback cho WebRTC
  - Thêm logic retry kết nối cho WebSockets
  - Thiết lập giám sát cho tính năng thời gian thực
  - Tạo kênh giao tiếp thay thế
- **Trạng thái**: Đã giảm thiểu thông qua xử lý lỗi mạnh mẽ

**Rủi ro 3: Giới hạn Dịch vụ AWS (THẤP-TRUNG BÌNH)**
- **Tác động**: Throttling ứng dụng hoặc dịch vụ không khả dụng
- **Khả năng**: THẤP
- **Giảm thiểu**:
  - Giám sát quotas dịch vụ trong CloudWatch
  - Triển khai rate limiting ở phía client
  - Yêu cầu tăng quota chủ động
  - Thiết kế cho graceful degradation
- **Trạng thái**: Được giám sát liên tục

**Rủi ro 4: Chi phí Xử lý AI (TRUNG BÌNH)**
- **Tác động**: Vượt ngân sách từ transcription và sử dụng LLM
- **Khả năng**: TRUNG BÌNH
- **Giảm thiểu**:
  - Triển khai usage caps và rate limiting
  - Cached AI responses trong DynamoDB
  - Sử dụng AI models tiết kiệm chi phí khi có thể
  - Giám sát chi tiêu thông qua AWS Cost Explorer
- **Trạng thái**: Kiểm soát thông qua giới hạn

**Rủi ro 5: Mất dữ liệu hoặc Hỏng (THẤP)**
- **Tác động**: Mất bản ghi cuộc họp hoặc dữ liệu workspace
- **Khả năng**: THẤP
- **Giảm thiểu**:
  - DynamoDB point-in-time recovery được bật
  - S3 versioning cho các tệp quan trọng
  - Xác thực backup thường xuyên
  - Triển khai Multi-AZ cho high availability
- **Trạng thái**: Được bảo vệ thông qua tính năng AWS

#### Rủi ro Quản lý Dự án

**Rủi ro 6: Thách thức Đường cong Học tập (TRUNG BÌNH - Đã xảy ra)**
- **Tác động**: Triển khai bị trở ngại do yêu cầu học AWS
- **Khả năng**: TRUNG BÌNH (Dự kiến cho thực tập)
- **Giảm thiểu Đã triển khai**:
  - Modules đào tạo AWS có cấu trúc (Tuần 1-7)
  - Labs thực hành trước công việc dự án
  - Hỗ trợ mentor và code reviews
  - Sự kiện cộng đồng để chia sẻ kiến thức
- **Tác động Thực tế**: Có thể quản lý được nhờ cách tiếp cận có cấu trúc

**Rủi ro 7: Scope Creep (THẤP)**
- **Tác động**: Dự án không hoàn thành trong thời gian thực tập
- **Khả năng**: THẤP
- **Giảm thiểu**:
  - Ưu tiên tính năng rõ ràng
  - Theo dõi milestone hàng tuần
  - Cách tiếp cận MVP-first
  - Giao tiếp stakeholder thường xuyên
- **Trạng thái**: Giữ đúng lịch trình thông qua lập kế hoạch kỷ luật

**Rủi ro 8: Độ phức tạp Tích hợp (TRUNG BÌNH - Đã xảy ra)**
- **Tác động**: Dịch vụ AWS không tích hợp suôn sẻ
- **Khả năng**: TRUNG BÌNH (Xảy ra ở Tuần 9)
- **Giảm thiểu Đã triển khai**:
  - Bắt đầu với tích hợp đơn giản trước
  - Kiểm tra toàn diện từng tích hợp
  - Tài liệu chi tiết về cấu hình
  - Hỗ trợ cộng đồng qua Discord/Study Group
- **Tác động Thực tế**: Vượt qua thông qua sự kiên trì và học tập

#### Rủi ro Bảo mật

**Rủi ro 9: Lỗ hổng Xác thực (TRUNG BÌNH)**
- **Tác động**: Truy cập trái phép vào workspaces và cuộc họp
- **Khả năng**: TRUNG BÌNH
- **Giảm thiểu**:
  - AWS Cognito cho xác thực cấp production
  - Xác thực JWT token trên tất cả endpoints
  - Kiểm soát truy cập dựa trên vai trò (RBAC)
  - Đánh giá bảo mật thường xuyên
- **Trạng thái**: Được bảo mật thông qua best practices AWS

**Rủi ro 10: Vấn đề Quyền riêng tư Dữ liệu (TRUNG BÌNH)**
- **Tác động**: Bản ghi cuộc họp và transcripts bị lộ
- **Khả năng**: TRUNG BÌNH
- **Giảm thiểu**:
  - Cô lập dữ liệu cấp workspace
  - S3 buckets được mã hóa (mã hóa khi lưu trữ)
  - HTTPS ở mọi nơi (mã hóa khi truyền tải)
  - IAM policies theo nguyên tắc least privilege
- **Trạng thái**: Triển khai thiết kế ưu tiên quyền riêng tư

---

### 8. Kết quả & Đầu ra Dự kiến

#### Đầu ra Kỹ thuật

**✅ Đã hoàn thành:**

1. **Ứng dụng Web Hoàn chỉnh**
   - Nền tảng workspace đa người thuê
   - Giao tiếp voice và text theo thời gian thực
   - Ghi âm và phiên âm cuộc họp
   - Phân tích cuộc họp được hỗ trợ bởi AI
   - Quản lý và theo dõi nhiệm vụ
   - Quản lý workspace và team

2. **Kiến trúc AWS Serverless**
   - Lambda functions cho tất cả logic backend
   - Thiết kế single-table DynamoDB
   - S3 storage với lifecycle policies
   - Triển khai CloudFront CDN
   - Xác thực Cognito
   - API Gateway REST endpoints

3. **Kho Source Code**
   - Frontend Next.js được tổ chức tốt
   - Backend Express.js trên Lambda
   - Cấu hình Infrastructure as Code (IaC)
   - Tài liệu README toàn diện
   - Scripts triển khai và CI/CD pipeline

4. **Triển khai trên AWS CloudFront**
   - Triển khai sẵn sàng production
   - Phân phối CDN toàn cầu
   - HTTPS với hỗ trợ custom domain
   - Chiến lược caching được tối ưu hóa
   - Giám sát và logging được bật

#### Kết quả Học tập

**✅ Kỹ năng AWS Đã đạt được:**

1. **Làm chủ Dịch vụ AWS Cốt lõi**
   - Lambda, DynamoDB, S3, CloudFront, Cognito
   - API Gateway, Step Functions, Transcribe
   - IAM, CloudWatch, Amplify
   - VPC, security groups và networking

2. **Thiết kế Kiến trúc Serverless**
   - Các mẫu kiến trúc event-driven
   - Thiết kế hệ thống đa người thuê
   - Chiến lược tối ưu hóa chi phí
   - Khả năng mở rộng và high availability

3. **Kinh nghiệm Dự án Thực tế**
   - Phát triển ứng dụng end-to-end
   - Triển khai và gỡ lỗi production
   - Tối ưu hóa hiệu suất
   - Xác thực và ủy quyền người dùng

4. **Kỹ năng Giải quyết Vấn đề**
   - Khắc phục sự cố triển khai CloudFront
   - WebRTC và giao tiếp thời gian thực
   - Thách thức tích hợp dịch vụ AWS
   - Kỹ thuật gỡ lỗi production

**✅ Phát triển Chuyên nghiệp:**

1. **Tham gia Cộng đồng**
   - Tham dự 4 sự kiện AWS chính
   - Tham gia AWS Certification Quiz Competition
   - Học từ 20+ diễn giả ngành
   - Xây dựng mạng lưới chuyên nghiệp trong cộng đồng cloud

2. **Giao tiếp Kỹ thuật**
   - Tài liệu worklog toàn diện (12 tuần)
   - Viết đề xuất kỹ thuật
   - Tài liệu theo dõi vấn đề và gỡ lỗi
   - Chia sẻ kiến thức với nhóm

3. **Quản lý Dự án**
   - Lập kế hoạch và theo dõi milestone
   - Quản lý thời gian qua 12 tuần
   - Nhận diện và giảm thiểu rủi ro
   - Giao tiếp stakeholder

#### Chỉ số Thành công Đo lường được

**Khả năng Nền tảng:**
- ✅ Hỗ trợ đa workspace (không giới hạn)
- ✅ Giao tiếp voice thời gian thực (độ trễ thấp)
- ✅ Text chat với lịch sử tin nhắn
- ✅ Ghi âm và phiên âm cuộc họp
- ✅ Phân tích cuộc họp được hỗ trợ bởi AI
- ✅ Trích xuất và quản lý nhiệm vụ
- ✅ Kiểm soát truy cập dựa trên vai trò
- ✅ Hệ thống mời thành viên

**Thành tựu Kỹ thuật:**
- ✅ Kiến trúc serverless (100% dựa trên Lambda)
- ✅ Hạ tầng tối ưu hóa chi phí ($44/tháng trung bình)
- ✅ Triển khai production trên CloudFront
- ✅ Thiết kế responsive (mobile và desktop)
- ✅ Hỗ trợ song ngữ (Tiếng Anh/Tiếng Việt)
- ✅ Xử lý lỗi và logging toàn diện

**Mốc Học tập:**
- ✅ Hoàn thành 7 modules đào tạo AWS
- ✅ Kinh nghiệm thực hành với 10+ dịch vụ AWS
- ✅ Tham dự 4 sự kiện cộng đồng AWS
- ✅ 12 tuần worklog được ghi chép
- ✅ Kinh nghiệm gỡ lỗi production
- ✅ Quyền sở hữu dự án end-to-end

---

### 9. Kết luận

#### Tóm tắt Dự án

**Nền tảng Quản lý Công việc từ Cuộc họp AI** thành công thể hiện sức mạnh của Kiến trúc AWS Serverless để xây dựng ứng dụng hiện đại, có khả năng mở rộng và tiết kiệm chi phí. Qua 12 tuần thực tập chuyên sâu tại FCJ (17/04 - 10/07/2026), dự án này đã phát triển từ việc học các kiến thức cơ bản về AWS đến một ứng dụng production được triển khai đầy đủ.

**Thành tựu Chính:**

1. **Xuất sắc Kỹ thuật**: Xây dựng ứng dụng serverless hoàn chỉnh sử dụng 10+ dịch vụ AWS với thiết kế kiến trúc phù hợp, tối ưu hóa chi phí và best practices bảo mật.

2. **Giải quyết Vấn đề Thực tế**: Giải quyết thách thức phổ biến của quy trình từ cuộc họp đến thực thi bằng cách tự động hóa phiên âm, phân tích và tạo nhiệm vụ.

3. **Triển khai Production**: Triển khai thành công lên AWS CloudFront với gỡ lỗi và giải quyết vấn đề toàn diện, thể hiện kỹ năng kỹ thuật production thực sự.

4. **Học tập Liên tục**: Tiến bộ từ cơ bản AWS (Tuần 1) đến kiến trúc serverless phức tạp, giao tiếp thời gian thực và tích hợp AI (Tuần 12).

5. **Tham gia Cộng đồng**: Tích cực tham gia 4 sự kiện AWS, học từ 20+ chuyên gia ngành và xây dựng mạng lưới chuyên nghiệp trong cộng đồng cloud.

#### Tác động & Giá trị

**Cho Tổ chức:**
- Thể hiện khả năng của intern để cung cấp ứng dụng sẵn sàng production
- Giới thiệu best practices cho kiến trúc serverless AWS
- Cung cấp các mẫu có thể tái sử dụng cho nền tảng SaaS đa người thuê
- Tạo cơ sở kiến thức thông qua tài liệu toàn diện

**Cho Tăng trưởng Cá nhân:**
- Làm chủ dịch vụ cốt lõi AWS và kiến trúc serverless
- Đạt được kinh nghiệm gỡ lỗi và giải quyết vấn đề production
- Phát triển kỹ năng quyền sở hữu dự án end-to-end
- Xây dựng nền tảng cho chứng chỉ AWS và phát triển sự nghiệp

**Cho Cộng đồng:**
- Worklog thực tập toàn diện phục vụ như tài nguyên học tập
- Ghi chép các thách thức và giải pháp thực tế
- Thể hiện ứng dụng thực tế của đào tạo AWS Study Group
- Tạo ví dụ về cách tiếp cận thực tập có cấu trúc

#### Cải tiến Tương lai

**Lộ trình Kỹ thuật:**
1. **Nâng cao Khả năng AI**
   - Hỗ trợ phiên âm đa ngôn ngữ
   - Phân tích sentiment cho cuộc họp
   - Ưu tiên action item tự động
   - Dự đoán kết quả cuộc họp

2. **Tính năng Nâng cao**
   - Tích hợp lịch (Google Calendar, Outlook)
   - Hỗ trợ ghi video cùng với audio
   - Bảng điều khiển phân tích và báo cáo nâng cao
   - Ứng dụng mobile cho iOS và Android

3. **Cải thiện Nền tảng**
   - Tích hợp SSO cho khách hàng doanh nghiệp
   - Tùy chỉnh workspace nâng cao
   - API cho tích hợp bên thứ ba
   - Nâng cao tính năng cộng tác thời gian thực

4. **Tối ưu hóa Hạ tầng**
   - Triển khai AWS Step Functions cho workflows phức tạp
   - Thêm ElastiCache để cải thiện hiệu suất
   - Cân nhắc EventBridge cho tính năng event-driven
   - Khám phá AWS AppSync cho GraphQL APIs

#### Lời cảm ơn

Tôi muốn bày tỏ lòng biết ơn đến:

- **FCJ (First Cloud Journey)** đã cung cấp cơ hội thực tập đặc biệt này và tạo ra môi trường học tập hỗ trợ
- **AWS Study Group** cho tài liệu đào tạo toàn diện, labs thực hành và hỗ trợ cộng đồng
- **Mentors và Supervisors** cho sự hướng dẫn, code reviews và chia sẻ kiến thức trong suốt thời gian thực tập
- **Diễn giả Sự kiện** (20+ chuyên gia) đã chia sẻ chuyên môn của họ tại AWS Study Group Workshop, FCAJ Community Days và AWS Quiz Competition
- **Bạn cùng Thực tập** cho sự cộng tác, công việc dự án nhóm và hỗ trợ lẫn nhau trong hành trình học tập
- **Cộng đồng AWS** trên Discord Lamentum cho thảo luận kỹ thuật, trợ giúp khắc phục sự cố và khích lệ

#### Suy ngẫm Cuối cùng

Kỳ thực tập này là một trải nghiệm học tập mang tính chuyển đổi. Bắt đầu với các kiến thức cơ bản về AWS và tiến bộ đến việc triển khai ứng dụng serverless production thể hiện sức mạnh của học tập có cấu trúc kết hợp với công việc dự án thực hành.

Các thách thức phải đối mặt—đặc biệt là các vấn đề triển khai CloudFront ở Tuần 11—là cơ hội học tập vô giá đã dạy tôi sự khác biệt giữa phát triển local và triển khai production. Các sự kiện cộng đồng AWS cung cấp thông tin chi tiết từ các nhà lãnh đạo ngành đã hình thành sự hiểu biết của tôi về kiến trúc cloud hiện đại.

Quan trọng nhất, dự án này đã chứng minh rằng với sự hướng dẫn phù hợp, học tập có cấu trúc và nỗ lực kiên trì, có thể xây dựng ứng dụng serverless cấp production ngay cả với tư cách là một intern. Tài liệu toàn diện trong worklog này phục vụ không chỉ là hồ sơ về kỳ thực tập của tôi, mà còn là tài nguyên cho những người học tương lai bắt đầu các hành trình tương tự.

---

### Phụ lục

#### A. Chi tiết Công nghệ

**Framework Frontend:**
- Next.js 16.2.9 (React 19)
- TailwindCSS 3.x
- TypeScript 5.x
- Socket.io Client 4.x
- WebRTC API

**Framework Backend:**
- Express.js 4.x
- AWS SDK v3
- TypeScript với strict mode
- WebRTC signaling server tùy chỉnh

**Dịch vụ AWS:**
- Compute: Lambda
- Storage: S3, DynamoDB
- AI/ML: Transcribe
- Networking: API Gateway, CloudFront
- Security: Cognito, IAM
- Monitoring: CloudWatch
- Deployment: Amplify

**APIs Bên thứ ba:**
- Google Gemini / OpenAI cho LLM
- STUN/TURN servers cho WebRTC

#### B. Tài nguyên & Tham khảo

**Tài nguyên AWS Study Group:**
- [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)
- [Cloud Journey](https://cloudjourney.awsstudygroup.com)
- [Discord Lamentum](https://discord.gg/yjFh7dNq)
- [Cộng đồng Facebook](https://www.facebook.com/groups/awsstudygroupfcj/)

**Tài nguyên Dự án:**
- [GitHub Worklog Repository](https://github.com/NTL0210/FACJ_Worklog)
- Tài liệu AWS
- Tài liệu Next.js
- Tài liệu WebRTC

**Sự kiện Cộng đồng Đã tham dự:**
1. AWS Study Group Workshop (09/05/2026)
2. FCAJ Community Day (23/05/2026)
3. AWS Certification Quiz Competition (20/06/2026)
4. FCAJ Community Day - AI Agents & DevOps (27/06/2026)

#### C. Thông tin Liên hệ

**Intern:** Nguyễn Tấn Lộc  
**Mã số sinh viên:** 2001210889  
**Trường:** HUTECH (Đại học Công nghệ TP. Hồ Chí Minh)  
**Vị trí:** FCJ Cloud Intern  
**Thời gian:** 17/04/2026 - 10/07/2026 (12 tuần)  
**GitHub:** [NTL0210](https://github.com/NTL0210)  
**Website Worklog:** [https://ntl0210.github.io/NTL0210-FACJ_Worklog/](https://ntl0210.github.io/NTL0210-FACJ_Worklog/)

---

**Phiên bản Tài liệu:** 1.0  
**Cập nhật Lần cuối:** 10/07/2026  
**Trạng thái:** Nộp Cuối cùng
