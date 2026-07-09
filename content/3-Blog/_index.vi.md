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

## Tổng quan Blog

Trong suốt thời gian thực tập, tôi đã viết ba bài blog kỹ thuật toàn diện về các khía cạnh khác nhau của việc xây dựng ứng dụng serverless sẵn sàng cho production trên AWS:

### [3.1. Xây dựng Ứng dụng Serverless với AWS Lambda và DynamoDB](3.1-blog1/)
Tìm hiểu về thiết kế kiến trúc serverless, mẫu single-table DynamoDB, và best practices để xây dựng ứng dụng đa người thuê có khả năng mở rộng.

**Chủ đề chính:**
- Kỹ thuật tối ưu hóa AWS Lambda
- Thiết kế single-table DynamoDB
- Chiến lược giảm cold start
- Cách ly dữ liệu đa người thuê
- Tối ưu hóa chi phí

---

### [3.2. Giao tiếp Voice Thời gian thực với WebRTC trên AWS](3.2-blog2/)
Tìm hiểu sâu về triển khai hệ thống voice chat dựa trên WebRTC với signaling server tùy chỉnh trên hạ tầng AWS.

**Chủ đề chính:**
- Kiến trúc peer-to-peer WebRTC
- Triển khai signaling server
- NAT traversal với STUN/TURN
- Cấu hình WebSocket CloudFront
- Mẫu quản lý kết nối

---

### [3.3. Gỡ lỗi Vấn đề Production trên CloudFront CDN](3.3-blog3/)
Kinh nghiệm gỡ lỗi thực tế sửa các vấn đề production nghiêm trọng sau khi triển khai lên CloudFront CDN.

**Chủ đề chính:**
- Cấu hình authentication cookie
- Tính ổn định kết nối WebSocket
- Vấn đề CORS và cross-origin
- Gỡ lỗi IAM permissions
- Chiến lược giám sát production

---

## Tác động Kỹ thuật

Những bài blog này ghi lại các thách thức và giải pháp thực tế từ việc xây dựng AI Meeting Workforce Platform, cung cấp những hiểu biết có giá trị cho bất kỳ ai làm việc với kiến trúc serverless AWS, giao tiếp thời gian thực, hoặc triển khai CloudFront.

**Tổng số Độc giả:** Được chia sẻ với cộng đồng FCJ và AWS Study Group  
**Công nghệ được đề cập:** AWS Lambda, DynamoDB, WebRTC, CloudFront, API Gateway, Cognito  
**Thời gian Dự án:** 12 tuần (17/4 - 10/7/2026)
