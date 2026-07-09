---
title: "Gỡ lỗi Vấn đề Production trên CloudFront CDN"
date: 2025-01-15
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Gỡ lỗi Vấn đề Production trên CloudFront CDN

{{< figure src="/NTL0210-FACJ_Worklog/images/blog3.png" title="Quy trình Gỡ lỗi CloudFront" >}}

Tuần 11 trong thời gian thực tập tại FCJ của tôi rất căng thẳng—sau khi triển khai ứng dụng serverless lên CloudFront, nhiều vấn đề production xuất hiện. Bài viết này ghi lại hành trình gỡ lỗi và bài học rút ra từ triển khai production.

---

## Kịch bản Triển khai

Sau 10 tuần phát triển local, đã đến lúc triển khai lên production:
- **Frontend**: Next.js static site trên CloudFront + S3
- **Backend**: Express.js Lambda functions đằng sau API Gateway
- **Real-time**: WebRTC signaling server trên Lambda riêng
- **Authentication**: AWS Cognito với JWT tokens

Mọi thứ hoạt động hoàn hảo trong phát triển local (`localhost:3000`). Sau đó tôi triển khai lên CloudFront...

---

## Chuỗi Vấn đề

**Bốn Lỗi Production Nghiêm trọng:**
1. ❌ **Login thất bại** - Người dùng không thể xác thực
2. ❌ **Voice chat thất bại** - Kết nối WebRTC không thiết lập được
3. ❌ **Text chat không ổn định** - Tin nhắn bị mất hoặc trễ
4. ❌ **Workspace invitations thất bại** - Email mời không gửi được

---

## Vấn đề 1: Lỗi Xác thực Login

### Nguyên nhân Gốc:
- Backend đã đặt cookie với `domain=localhost`
- CloudFront domain không khớp
- CORS headers chưa đầy đủ

### Giải pháp:
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
```

**Kết quả:** ✅ Login hoạt động, cookies tồn tại qua các requests

---

## Vấn đề 2: Lỗi WebRTC Voice Chat

### Nguyên nhân Gốc:
- API Gateway WebSocket timeout (30 giây qua CloudFront)
- Thiếu WebSocket upgrade headers
- Origin sai cho STUN/TURN

### Giải pháp:
1. Cấu hình CloudFront WebSocket timeout lên 3600 giây
2. Triển khai cơ chế heartbeat ping/pong
3. Sử dụng public STUN servers trong production

**Kết quả:** ✅ Voice chat kết nối ổn định, WebSocket luôn sống

---

## Vấn đề 3: Text Chat Không Ổn định

### Nguyên nhân Gốc:
- Không có logic tự động reconnection
- CloudFront đóng kết nối WebSocket idle
- Không có queuing cho tin nhắn thất bại

### Giải pháp:
Triển khai tự động reconnection với exponential backoff và message queuing.

**Kết quả:** ✅ Tin nhắn gửi đi đáng tin cậy, tự động reconnection hoạt động

---

## Vấn đề 4: Lỗi Gửi Email Workspace Invitation

### Nguyên nhân Gốc:
Lambda function không thể gửi email qua AWS SES do thiếu IAM permissions.

### Giải pháp:
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

**Kết quả:** ✅ Email mời được gửi thành công

---

## Bài học Rút ra

1. **Local Development ≠ Production**
   - Luôn test trong môi trường staging phản ánh production
   - CloudFront có hành vi khác với localhost

2. **Cấu hình Cookie rất Quan trọng**
   - Cài đặt `domain`, `sameSite`, `secure` là then chốt
   - Production cần `sameSite: 'none'` cho cross-origin cookies

3. **WebSocket qua CDN Khó**
   - CloudFront có idle timeout mặc định 30 giây
   - Triển khai heartbeat ping/pong để giữ kết nối sống

4. **Luôn Triển khai Logic Reconnection**
   - Mạng không đáng tin cậy, lên kế hoạch cho disconnections
   - Exponential backoff ngăn server quá tải

5. **IAM Permissions Không Tùy chọn**
   - Mỗi dịch vụ AWS cần permissions rõ ràng
   - Giám sát CloudWatch logs cho permission errors

6. **Giám sát Mọi thứ trong Production**
   - CloudWatch logs đã tiết kiệm thời gian gỡ lỗi
   - Giám sát thời gian thực giúp phát hiện vấn đề sớm

---

## Bộ công cụ Gỡ lỗi

**Công cụ Cần thiết Đã sử dụng:**
1. **Browser DevTools** - Tab Network, tab Application, Console
2. **AWS CloudWatch** - Lambda logs, API Gateway logs, Custom metrics
3. **Postman / cURL** - Test API endpoints trực tiếp
4. **WebSocket Test Clients** - Test kết nối WebSocket độc lập

---

## Kết luận

Triển khai lên CloudFront đã dạy tôi rằng **production là một thế giới khác**. Các vấn đề mà tôi gặp phải—authentication cookies, kết nối WebRTC, tính ổn định WebSocket, và quyền email—tất cả đều vô hình trong phát triển local nhưng quan trọng trong production.

Bài học quan trọng nhất: **Luôn có môi trường staging** phản ánh production. Test trên CloudFront staging trước sẽ đã phát hiện những vấn đề này trước khi chúng ảnh hưởng người dùng.

Tuần 11 rất khó khăn, nhưng gỡ lỗi các vấn đề production này đã cho tôi kinh nghiệm vô giá về:
- Phương pháp troubleshooting production
- Hành vi và cấu hình CDN
- Hạ tầng giao tiếp thời gian thực
- Tích hợp dịch vụ AWS trong production

Những bài học này sẽ ở lại với tôi trong suốt sự nghiệp của tôi trong cloud engineering.

---

## Bài viết Liên quan

- [Xây dựng Ứng dụng Serverless với AWS Lambda và DynamoDB](../3.1-blog1/)
- [Giao tiếp Voice Thời gian thực với WebRTC trên AWS](../3.2-blog2/)

---

*Được viết trong 12 tuần thực tập tại FCJ (17/4 - 10/7/2026) như một phần của dự án AI Meeting Workforce Platform.*
