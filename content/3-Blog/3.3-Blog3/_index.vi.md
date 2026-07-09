---
title: "Gỡ lỗi Vấn đề Production trên CloudFront CDN"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Gỡ lỗi Vấn đề Production trên CloudFront CDN

{{< figure src="/NTL0210-FACJ_Worklog/images/blog3.png" title="Quy trình Triển khai và Gỡ lỗi CloudFront" >}}

## Giới thiệu

Tuần 11 của thực tập là tuần căng thẳng nhất. Sau khi triển khai ứng dụng lên AWS CloudFront, tôi gặp phải nhiều vấn đề quan trọng: lỗi đăng nhập, lỗi voice chat, vấn đề text messaging và lỗi mời workspace. Bài viết này ghi chép hành trình gỡ lỗi, phân tích nguyên nhân gốc và các giải pháp đã dạy tôi sự khác biệt giữa phát triển local và triển khai production.

## Triển khai

Sau 10 tuần phát triển và kiểm tra local, mọi thứ hoạt động hoàn hảo trên `localhost`. Ứng dụng có xác thực người dùng, voice chat thời gian thực, text messaging và mời thành viên workspace.

Tự tin vào code của mình, tôi triển khai lên CloudFront CDN. Đó là khi mọi thứ bắt đầu hỏng.

## Vấn đề 1: Lỗi Đăng nhập

### Vấn đề
Người dùng không thể đăng nhập sau triển khai CloudFront. Form đăng nhập submit thành công nhưng authentication tokens không được lưu. Refresh trang? Đăng xuất lại.

### Nguyên nhân Gốc
Vấn đề là **SameSite=Strict** cookie policy kết hợp với CloudFront domain mismatch. Cookie với `SameSite=Strict` bị từ chối cross-site requests.

### Giải pháp
Thay đổi cấu hình cookie sang `SameSite=none`, thêm wildcard domain và cấu hình CORS phù hợp cho CloudFront.

### Bài học
**Cookie security phức tạp với CDNs.** Những gì hoạt động locally không phải lúc nào cũng hoạt động trong production với domains khác nhau và CDN layers.

## Vấn đề 2: Lỗi Voice Chat

### Vấn đề
Voice chat hoàn toàn thất bại trên CloudFront. Người dùng có thể join channels nhưng không có audio transmission.

### Nguyên nhân Gốc
Nhiều vấn đề gộp lại:
1. **Signaling Server**: Signaling server phía sau CloudFront với cấu hình WebSocket sai
2. **STUN/TURN**: ICE servers được cấu hình với localhost addresses
3. **Mixed Content**: CloudFront sử dụng HTTPS, signaling server là HTTP

### Giải pháp
- Triển khai signaling server riêng biệt (không qua CloudFront)
- Cập nhật ICE server configuration với production domains
- Đảm bảo HTTPS ở mọi nơi

### Bài học
**Real-time communication cần cân nhắc CDN đặc biệt.** WebSocket connections, STUN/TURN servers và signaling paths phải được cấu hình cho production domains.

## Vấn đề 3: Text Chat Message Delivery

### Vấn đề
Text messages xuất hiện không nhất quán. Đôi khi messages đến, đôi khi không. Thứ tự messages ngẫu nhiên.

### Nguyên nhân Gốc
- **WebSocket Instability**: CloudFront timeout settings quá aggressive
- **Không có Reconnection Logic**: Client không xử lý disconnects gracefully
- **Thiếu Message Queue**: Không có cơ chế retry cho failed sends
- **Không có Acknowledgments**: Không thể xác nhận message delivery

### Giải pháp
- Triển khai reconnection mạnh mẽ
- Thêm message queue với acknowledgments
- Client-side message ordering

### Bài học
**Real-time messaging cần reliability layers.** Network issues phổ biến trong production. Triển khai reconnection, acknowledgments và message queues từ đầu.

## Vấn đề 4: Lỗi Mời Workspace

### Vấn đề
Workspace invitation emails không được gửi. Tính năng hoạt động hoàn hảo locally nhưng thất bại silent trong production.

### Nguyên nhân Gốc
AWS SES ở **sandbox mode**, giới hạn:
- Chỉ có thể gửi đến địa chỉ email đã verified
- Chỉ có thể gửi từ địa chỉ email đã verified
- Giới hạn 200 emails mỗi ngày

### Giải pháp
- Yêu cầu SES Production Access
- Thêm error handling phù hợp
- Sửa invitation link validation (sử dụng domain thực thay vì localhost)
- Triển khai retry mechanism

### Bài học
**Test với cấu hình giống production.** Sandbox mode, localhost URLs và verified emails che giấu vấn đề production thực sự.

## Chiến lược Gỡ lỗi Chung

### 1. Logging Toàn diện
Structured logging với context đầy đủ giúp debug nhanh hơn.

### 2. Feature Flags
Kích hoạt dần dần tính năng trong production.

### 3. Environment Parity
Cấu hình giống nhau cho tất cả môi trường.

### 4. Monitoring và Alerts
CloudWatch metrics để theo dõi system health.

## Bài học Chính từ Tuần 11

1. **Local testing không đủ**: Luôn triển khai lên staging environment giống production

2. **CDN thay đổi mọi thứ**: Cookies, WebSockets và CORS hoạt động khác với CloudFront

3. **Monitoring quan trọng**: Không thể sửa những gì bạn không thấy

4. **Error handling quan trọng**: Silent failures là tệ nhất

5. **Đọc documentation**: AWS services có gotchas (SES sandbox mode, CloudFront WebSocket limits)

6. **Test từng bước**: Triển khai một tính năng một lúc để cô lập vấn đề

7. **Có kế hoạch rollback**: Giữ phiên bản hoạt động trước đó sẵn sàng

## Công cụ Đã cứu tôi

### Browser DevTools:
- **Network tab**: Kiểm tra headers, timing và failures
- **Console**: Kiểm tra lỗi và WebRTC connection states
- **Application tab**: Xem cookies và localStorage

### AWS Tools:
- **CloudWatch Logs**: Centralized logging
- **CloudWatch Metrics**: Theo dõi system health
- **X-Ray**: Trace requests
- **SES Console**: Giám sát email delivery

## Kết luận

Tuần 11 là tuần thách thức nhất nhưng cũng là tuần mang tính giáo dục nhất. Gỡ lỗi production issues dạy tôi nhiều hơn về kỹ thuật phần mềm thực tế hơn 10 tuần phát triển.

Sự khác biệt giữa "it works on my machine" và "it works in production" là rất lớn. Hiểu CDN behavior, cookie policies, WebSocket configuration và AWS service limitations là quan trọng cho triển khai ứng dụng sẵn sàng production.

## Tài nguyên

- [AWS CloudFront Documentation](https://docs.aws.amazon.com/cloudfront/)
- [MDN: SameSite Cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie/SameSite)
- [WebRTC Troubleshooting Guide](https://webrtc.github.io/samples/)
- [AWS SES Sandbox Mode](https://docs.aws.amazon.com/ses/latest/dg/request-production-access.html)
- [Project GitHub Repository](https://github.com/NTL0210/FACJ_Worklog)

---

**Tác giả:** Nguyễn Tấn Lộc  
**Ngày:** 10/07/2026  
**Tags:** CloudFront, Debugging, Production Issues, AWS, CDN, WebRTC, SES, CORS, Cookies
