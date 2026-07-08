---
title: "Worklog Tuần 11"
date: 2026-06-29
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Mục tiêu tuần 11:

* Tiếp tục phát triển dự án nhóm
* Triển khai ứng dụng lên CloudFront để test
* Khắc phục và sửa các lỗi liên quan đến deployment
* Debug các vấn đề môi trường production
* Đảm bảo tính ổn định ứng dụng trên hạ tầng cloud

### Công việc đã hoàn thành:

| Ngày | Công việc | Kết quả | Tài liệu | Ghi chú |
|------|-----------|---------|----------|---------|
| 29/06/2026 | - Tiếp tục phát triển dự án<br>- Chuẩn bị triển khai CloudFront<br>- Thiết lập deployment ban đầu | - Hoàn thành các tác vụ phát triển tính năng<br>- Cấu hình CloudFront distribution<br>- Triển khai phiên bản đầu tiên lên CloudFront<br>- Phát hiện các vấn đề deployment | [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)<br>[Cloud Journey](https://cloudjourney.awsstudygroup.com)<br>[Discord Lamentum](https://discord.gg/yjFh7dNq)<br>[GitHub Worklog](https://github.com/NTL0210/FACJ_Worklog) | Bắt đầu triển khai CloudFront |
| 30/06/2026 | - Debug lỗi chức năng đăng nhập<br>- Điều tra vấn đề xác thực<br>- Test đăng nhập trên CloudFront | - Xác định lỗi đăng nhập trên CloudFront<br>- Debug luồng xác thực<br>- Tìm thấy vấn đề CORS và cookie<br>- Làm việc để sửa xác thực | [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)<br>[Cloud Journey](https://cloudjourney.awsstudygroup.com)<br>[Discord Lamentum](https://discord.gg/yjFh7dNq)<br>[GitHub Worklog](https://github.com/NTL0210/FACJ_Worklog) | Khắc phục lỗi đăng nhập |
| 01/07/2026 | - Sửa chức năng voice chat<br>- Debug kết nối WebRTC<br>- Test voice chat trên CloudFront | - Phát hiện lỗi voice chat<br>- Điều tra vấn đề WebRTC signaling<br>- Sửa vấn đề media stream<br>- Vẫn đang khắc phục tính ổn định kết nối | [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)<br>[Cloud Journey](https://cloudjourney.awsstudygroup.com)<br>[Discord Lamentum](https://discord.gg/yjFh7dNq)<br>[GitHub Worklog](https://github.com/NTL0210/FACJ_Worklog) | Debug voice chat |
| 02/07/2026 | - Debug tin nhắn kênh text chat<br>- Sửa vấn đề gửi tin nhắn<br>- Test tin nhắn thời gian thực | - Xác định vấn đề gửi tin nhắn text<br>- Debug kết nối WebSocket<br>- Sửa vấn đề đồng bộ tin nhắn<br>- Cải thiện giao tiếp real-time | [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)<br>[Cloud Journey](https://cloudjourney.awsstudygroup.com)<br>[Discord Lamentum](https://discord.gg/yjFh7dNq)<br>[GitHub Worklog](https://github.com/NTL0210/FACJ_Worklog) | Lỗi text chat |
| 03/07/2026 | - Sửa lỗi mời thành viên workspace<br>- Debug hệ thống invitation<br>- Test tính năng quản lý thành viên | - Phát hiện lỗi chức năng mời<br>- Debug hệ thống email invitation<br>- Sửa vấn đề permission thành viên<br>- Test quy trình mời | [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)<br>[Cloud Journey](https://cloudjourney.awsstudygroup.com)<br>[Discord Lamentum](https://discord.gg/yjFh7dNq)<br>[GitHub Worklog](https://github.com/NTL0210/FACJ_Worklog) | Debug hệ thống invitation |
| 04/07/2026 | - Tiếp tục sửa lỗi CloudFront<br>- Test toàn diện<br>- Sửa bug và tối ưu | - Sửa nhiều vấn đề deployment<br>- Cải thiện tính ổn định ứng dụng<br>- Thực hiện test mở rộng<br>- Ghi chép tất cả bug và giải pháp | [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)<br>[Cloud Journey](https://cloudjourney.awsstudygroup.com)<br>[Discord Lamentum](https://discord.gg/yjFh7dNq)<br>[GitHub Worklog](https://github.com/NTL0210/FACJ_Worklog) | Marathon sửa bug |
| 05/07/2026 | - Test cuối và xác thực<br>- Đánh giá nhóm về các fix<br>- Retrospective hàng tuần | - Xác thực tất cả fix chính<br>- Thực hiện đánh giá nhóm<br>- Ghi chép bài học kinh nghiệm<br>- Lập kế hoạch công việc tuần sau | [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)<br>[Cloud Journey](https://cloudjourney.awsstudygroup.com)<br>[Discord Lamentum](https://discord.gg/yjFh7dNq)<br>[GitHub Worklog](https://github.com/NTL0210/FACJ_Worklog) | Tổng kết tuần |


### Kiến thức đã học:

* **Triển khai CloudFront**: Hiểu các thách thức khi triển khai ứng dụng web lên CloudFront CDN
* **Debug Production**: Phát triển kỹ năng debug các vấn đề chỉ xuất hiện trong môi trường production
* **Xác thực trong CDN**: Học về CORS, cookie và thách thức xác thực với CDN
* **Khắc phục WebRTC**: Có kinh nghiệm debug voice chat và vấn đề kết nối WebRTC
* **WebSocket với CloudFront**: Hiểu hành vi WebSocket qua CloudFront distribution
* **Giao tiếp Real-time**: Debug các tính năng real-time trong môi trường cloud phân tán

### Thành tựu:

* Triển khai thành công ứng dụng lên CloudFront lần đầu tiên
* Xác định và ghi chép tất cả vấn đề deployment chính
* Sửa các vấn đề xác thực đăng nhập nghiêm trọng
* Giải quyết vấn đề kết nối WebRTC của voice chat
* Sửa vấn đề gửi tin nhắn text chat
* Khắc phục chức năng mời thành viên workspace
* Cải thiện tính ổn định tổng thể ứng dụng trên CloudFront
* Xây dựng tài liệu toàn diện về vấn đề và giải pháp

### Khó khăn & Giải pháp:

* **Khó khăn**: Chức năng đăng nhập bị hỏng sau khi triển khai CloudFront
  * **Nguyên nhân gốc**: Vấn đề cấu hình CORS, vấn đề domain của cookie, xử lý authentication token
  * **Giải pháp**: Cập nhật CORS header, cấu hình cookie setting cho CDN, sửa lưu trữ token

* **Khó khăn**: Voice chat không hoạt động trên CloudFront
  * **Nguyên nhân gốc**: Vấn đề kết nối WebRTC signaling server, cấu hình STUN/TURN server, định tuyến media stream
  * **Giải pháp**: Cấu hình lại WebRTC signaling, cập nhật cài đặt STUN/TURN, sửa xử lý media stream

* **Khó khăn**: Tin nhắn text chat không gửi đúng
  * **Nguyên nhân gốc**: Kết nối WebSocket không ổn định qua CloudFront, vấn đề đồng bộ message queue
  * **Giải pháp**: Cải thiện xử lý kết nối WebSocket, triển khai logic retry tin nhắn, sửa đồng bộ

* **Khó khăn**: Mời thành viên workspace bị lỗi
  * **Nguyên nhân gốc**: Vấn đề tích hợp email service, vấn đề tạo invitation link, lỗi quản lý permission
  * **Giải pháp**: Sửa cấu hình email service, khắc phục tạo URL invitation, giải quyết gán permission


### Các vấn đề kỹ thuật gặp phải:

#### 1. Lỗi đăng nhập trên CloudFront

**Triệu chứng:**
- Người dùng không thể đăng nhập sau khi triển khai CloudFront
- Authentication token không được lưu đúng
- Lỗi quản lý session
- Vấn đề redirect sau khi đăng nhập

**Quá trình Debug:**
- Kiểm tra browser console để tìm lỗi
- Phân tích network request và response
- Kiểm tra xử lý cookie và token
- Xem xét hành vi cache của CloudFront

**Nguyên nhân gốc:**
- CORS header không cấu hình đúng cho CloudFront
- Cookie không được set với domain và security flag phù hợp
- API endpoint xác thực bị cache không đúng cách
- Lưu trữ token xung đột với hành vi CDN

**Giải pháp đã triển khai:**
- Cập nhật cấu hình CORS cho CloudFront distribution
- Set cookie flag phù hợp (SameSite, Secure, Domain)
- Cấu hình cache behavior để bypass endpoint xác thực
- Sửa lưu trữ token để hoạt động với kiến trúc CDN

#### 2. Lỗi chức năng Voice Chat

**Triệu chứng:**
- Kết nối voice chat không thiết lập được
- Audio không truyền giữa người dùng
- Kết nối bị drop và không ổn định
- Lỗi WebRTC signaling

**Quá trình Debug:**
- Kiểm tra log kết nối WebRTC
- Test kết nối STUN/TURN server
- Phân tích giao tiếp signaling server
- Kiểm tra khởi tạo media stream

**Nguyên nhân gốc:**
- WebRTC signaling server không truy cập được qua CloudFront
- Cấu hình STUN/TURN server không tương thích với CDN
- Vấn đề định tuyến media stream trong môi trường phân tán
- Lỗi thu thập ICE candidate

**Giải pháp đã triển khai:**
- Cấu hình lại endpoint WebRTC signaling server
- Cập nhật cài đặt STUN/TURN server cho tương thích CDN
- Triển khai xử lý ICE candidate đúng đắn
- Thêm cơ chế fallback kết nối

#### 3. Vấn đề gửi tin nhắn Text Chat

**Triệu chứng:**
- Tin nhắn không xuất hiện trong kênh chat
- Thứ tự tin nhắn không nhất quán
- Thông báo tin nhắn bị delay hoặc mất
- Vấn đề đồng bộ real-time

**Quá trình Debug:**
- Giám sát tính ổn định kết nối WebSocket
- Theo dõi luồng tin nhắn từ người gửi đến người nhận
- Kiểm tra message queue và log database
- Phân tích logic đồng bộ real-time

**Nguyên nhân gốc:**
- Kết nối WebSocket không ổn định qua CloudFront
- Gửi tin nhắn không xử lý latency CDN đúng cách
- Logic đồng bộ không tính đến kiến trúc phân tán
- Thiếu cơ chế retry và acknowledgment tin nhắn

**Giải pháp đã triển khai:**
- Cải thiện tính ổn định và reconnection WebSocket
- Triển khai logic acknowledgment và retry tin nhắn
- Nâng cao đồng bộ cho môi trường phân tán
- Thêm message queue client-side với giải quyết xung đột

#### 4. Lỗi mời Workspace

**Triệu chứng:**
- Email invitation không được gửi
- Link invitation không hoạt động đúng
- Thành viên mới không thể tham gia workspace
- Lỗi gán permission

**Quá trình Debug:**
- Kiểm tra log email service
- Test tạo invitation link
- Xác minh logic gán permission
- Phân tích kiểm soát truy cập workspace

**Nguyên nhân gốc:**
- Tích hợp email service bị ảnh hưởng bởi CloudFront deployment
- URL invitation được tạo với domain không đúng
- Vấn đề timing gán permission trong hệ thống phân tán
- Kiểm soát truy cập workspace không xử lý CDN đúng cách

**Giải pháp đã triển khai:**
- Cấu hình lại email service cho môi trường CDN
- Sửa tạo invitation URL với domain đúng
- Cải thiện timing và xác minh gán permission
- Cập nhật logic kiểm soát truy cập workspace

### Bài học kinh nghiệm:

1. **Khác biệt Local vs Production**: Nhiều vấn đề chỉ xuất hiện trong môi trường CloudFront production mà hoạt động tốt ở local

2. **Độ phức tạp CDN**: Triển khai ứng dụng real-time có xác thực lên CDN đưa ra độ phức tạp đáng kể

3. **Chiến lược Testing**: Cần chiến lược testing toàn diện cho triển khai CDN trước khi lên production

4. **Tầm quan trọng tài liệu**: Ghi chép mỗi vấn đề và giải pháp là cực kỳ quan trọng cho khắc phục sự cố tương lai

5. **Kỹ năng Debug**: Debug production cần cách tiếp cận khác với debug phát triển local

6. **Hợp tác nhóm**: Vấn đề production phức tạp cần debug hợp tác và chia sẻ kiến thức

### Tài liệu tham khảo:

* [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)
* [Cloud Journey](https://cloudjourney.awsstudygroup.com)
* [Discord Lamentum](https://discord.gg/yjFh7dNq)
* [GitHub Worklog Repository](https://github.com/NTL0210/FACJ_Worklog)
* Tài liệu AWS CloudFront
* Hướng dẫn khắc phục WebRTC
* Best Practices WebSocket với CDN

