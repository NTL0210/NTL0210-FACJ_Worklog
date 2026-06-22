---
title: "Sự kiện 3: Cuộc thi AWS Certification Quiz"
date: 2026-06-20
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---

## Thông tin sự kiện

**Tên sự kiện:** Cuộc thi AWS Certification Quiz  
**Ngày giờ:** 20 tháng 6, 2026  
**Hình thức:** Thi đấu đội (8 đội)  
**Vai trò:** Người tham gia/Quan sát  
**Chủ trì:** Cộng đồng AWS Study Group

{{< figure src="/NTL0210-FACJ_Worklog/images/123.jpg" title="Cuộc thi AWS Certification Quiz 2026" >}}

## Ảnh sự kiện

{{< figure src="/NTL0210-FACJ_Worklog/images/1234.jpg" title="Khoảnh khắc thi đấu 1" >}}

{{< figure src="/NTL0210-FACJ_Worklog/images/12345.jpg" title="Khoảnh khắc thi đấu 2" >}}

## Tổng quan sự kiện

Cuộc thi AWS Certification Quiz là một cuộc thi kiến thức hấp dẫn với 8 đội thi đấu với nhau để trả lời các câu hỏi về dịch vụ AWS cloud và các khái niệm cloud computing nói chung. Sự kiện được dẫn dắt bởi Anh Thịnh, người đã cung cấp những thông tin chi tiết và kiến thức bổ sung quý giá về các khái niệm AWS trong suốt cuộc thi.

## Thể thức thi đấu

### Cấu trúc vòng thi

Cuộc thi tiến hành qua nhiều vòng:

**1. Vòng loại**
- Sàng lọc ban đầu của tất cả 8 đội
- Các câu hỏi về kiến thức AWS cơ bản
- Các đội thể hiện trình độ kiến thức AWS khác nhau

**2. Bán kết**
- Các đội hàng đầu tiến vào thi đấu
- Các câu hỏi khó hơn về dịch vụ AWS
- Độ khó và phức tạp tăng lên

**3. Chung kết** *(Hoãn lại)*
- Do thời gian có hạn, vòng chung kết được hoãn sang tuần sau
- Cho phép các đội chuẩn bị và học thêm
- Tạo sự mong đợi cho vòng tranh chức vô địch

### Động lực thi đấu

Một quan sát thú vị: các đội dường như dựa vào trực giác và dự đoán có cơ sở ("chơi hệ tâm linh") hơn là kiến thức kỹ thuật thuần túy, điều này khiến cuộc thi thêm phần thú vị và khó đoán!

## Chủ đề chính được đề cập

### 1. Kiến thức chung về AWS
- Các dịch vụ AWS cốt lõi và trường hợp sử dụng
- Kiến thức cơ bản về cloud computing
- Best practices của AWS
- Tích hợp các dịch vụ

### 2. Tình huống AWS thực tế
Các câu hỏi bao gồm nhiều tình huống thực tế, thực tế kiểm tra:
- Kỹ năng giải quyết vấn đề
- Hiểu biết về khả năng của dịch vụ AWS
- Chiến lược tối ưu chi phí
- Best practices về bảo mật

### 3. Phân quyền theo Resource vs Identity

**Chủ đề nổi bật:** Một trong những câu hỏi thú vị và thực tế nhất tập trung vào **ưu tiên phân quyền theo resource (resource-based policies) thay vì theo identity (identity-based policies)**.

#### Tại sao nên dùng Resource-Based Policies?

**Ưu điểm của Resource-Based Policies:**
- **Cross-Account Access**: Dễ dàng cấp quyền truy cập tài nguyên từ các AWS account khác
- **Quản lý tập trung**: Phân quyền được định nghĩa trực tiếp trên tài nguyên
- **Đơn giản hóa quản lý**: Không cần sửa đổi policy của user/role
- **Kiểm soát rõ ràng**: Dễ dàng nhìn thấy ai có thể truy cập tài nguyên
- **Không cần Assume Role**: Truy cập trực tiếp mà không cần assume role

**Trường hợp sử dụng phổ biến:**
- **S3 Bucket Policies**: Kiểm soát truy cập bucket và object
- **SQS Queue Policies**: Quản lý phân quyền message queue
- **SNS Topic Policies**: Cấu hình quyền truy cập topic
- **Lambda Function Policies**: Kiểm soát phân quyền gọi function
- **KMS Key Policies**: Quản lý quyền truy cập encryption key

**Ví dụ tình huống:**
Khi bạn muốn cho phép nhiều account hoặc service truy cập S3 bucket, hiệu quả hơn là:
- ✅ Thêm bucket policy (resource-based) cho phép các account đó
- ❌ Thay vì sửa IAM policy (identity-based) ở mỗi account

#### Identity-Based vs Resource-Based Policies

**Identity-Based Policies:**
- Gắn vào IAM user, group, hoặc role
- Kiểm soát identity có thể làm gì
- Phải quản lý ở mỗi account

**Resource-Based Policies:**
- Gắn trực tiếp vào resource (S3, SQS, SNS, v.v.)
- Kiểm soát ai có thể truy cập resource
- Quản lý tập trung trên resource

**Best Practice:** Sử dụng resource-based policies khi cần cấp quyền cross-account hoặc muốn quản lý phân quyền tập trung.

## Kiến thức từ Anh Thịnh

Trong suốt cuộc thi, Anh Thịnh đã cung cấp thông tin bổ sung và ngữ cảnh có giá trị cho mỗi câu hỏi:

### Kiến thức kỹ thuật
- **Ứng dụng thực tế**: Các khái niệm áp dụng như thế nào trong môi trường production
- **Lỗi thường gặp**: Những cạm bẫy cần tránh khi triển khai AWS services
- **Best practices**: Các phương pháp chuẩn trong ngành về kiến trúc AWS
- **Cân nhắc chi phí**: Các giải pháp khác nhau ảnh hưởng đến chi phí AWS như thế nào
- **Vấn đề bảo mật**: Các khía cạnh bảo mật của cấu hình AWS

### Phương pháp học
- **Hiểu thay vì học vẹt**: Tập trung vào khái niệm hơn là học thuộc lòng
- **Kinh nghiệm thực tế**: Tầm quan trọng của thực hành hands-on với AWS services
- **Tư duy tình huống**: Áp dụng kiến thức vào vấn đề thực tế
- **Tối ưu chi phí**: Luôn xem xét hiệu quả chi phí trong giải pháp

## Bài học chính

### 1. Quản lý phân quyền AWS
- Hiểu sâu về resource-based vs identity-based policies
- Khi nào sử dụng từng loại policy
- Mô hình cross-account access
- Best practices bảo mật cho phân quyền

### 2. Kiến thức AWS thực tế
- Tình huống thực tế có giá trị hơn kiến thức lý thuyết
- Hiểu tương tác giữa các service là rất quan trọng
- Chi phí và bảo mật phải được xem xét trong mọi quyết định

### 3. Kỹ năng thi đấu đội
- Tư duy nhanh dưới áp lực
- Giải quyết vấn đề hợp tác
- Dự đoán chiến lược khi không chắc chắn ("hệ tâm linh"!)
- Học hỏi từ cách tiếp cận của đội khác

### 4. Học tập liên tục
- AWS rất rộng và không ngừng phát triển
- Sự kiện cộng đồng cung cấp cơ hội học tập quý giá
- Câu hỏi thực tế tiết lộ khoảng trống kiến thức
- Câu hỏi cấp độ certification kiểm tra hiểu biết sâu

## Suy nghĩ cá nhân

### Phát triển kỹ thuật
Cuộc thi này nhấn mạnh một số lĩnh vực cần cải thiện:
- **Kiến trúc phân quyền**: Cần hiểu sâu hơn về IAM và resource policies
- **Tích hợp service**: Các AWS service khác nhau hoạt động cùng nhau như thế nào
- **Best Practices**: Phương pháp chuẩn cho các vấn đề phổ biến
- **Ứng dụng thực tế**: Chuyển đổi kiến thức lý thuyết sang tình huống thực

### Trải nghiệm cạnh tranh
- **Kiểm tra dưới áp lực**: Trả lời câu hỏi dưới áp lực thời gian tiết lộ hiểu biết thực sự
- **Khoảng trống kiến thức**: Cuộc thi phơi bày các lĩnh vực cần học thêm
- **Học từ người khác**: Quan sát các phương pháp giải quyết vấn đề khác nhau từ đội khác
- **Yếu tố vui**: Phương pháp "dựa vào trực giác" khiến cuộc thi giải trí và bớt căng thẳng!

### Giá trị cộng đồng
- **Hướng dẫn chuyên gia**: Giải thích bổ sung của Anh Thịnh vô cùng có giá trị
- **Học hỏi lẫn nhau**: Học từ câu trả lời và lý luận của đội khác
- **Kết nối**: Kết nối với những người đam mê AWS khác
- **Động lực**: Thể thức thi đấu tạo động lực học AWS sâu hơn

## Quan sát thú vị

### "Hệ tâm linh"
Các đội dường như dựa nhiều vào dự đoán có học thức và trực giác:
- **Nhận dạng mẫu**: Nhận ra các mẫu AWS phổ biến
- **Chiến lược loại trừ**: Loại bỏ các câu trả lời rõ ràng sai
- **Dự đoán may mắn**: Đôi khi đúng câu trả lời mà không hoàn toàn tự tin
- **Hóa học nhóm**: Các nhóm hoạt động tốt với nhau dường như may mắn hơn!

Cách tiếp cận này, tuy hài hước, thực sự chứng minh:
- Hiểu biết nền tảng vững chắc cho phép trực giác tốt
- Kinh nghiệm AWS xây dựng nhận dạng mẫu
- Tự tin trong dự đoán có học thức là kỹ năng quý giá
- Làm việc nhóm có thể bù đắp cho khoảng trống kiến thức cá nhân

## Hành động cần làm

### Trọng tâm học tập ngay
1. **Thành thạo IAM Policies**: Tìm hiểu sâu về resource-based vs identity-based policies
2. **Cross-Account Access**: Học các mô hình và best practices cho cross-account access
3. **Tích hợp Service**: Hiểu cách các AWS service khác nhau tương tác
4. **Tình huống thực tế**: Thực hành với các vấn đề kiến trúc AWS thực tế

### Phát triển dài hạn
1. **AWS Certification**: Cân nhắc chuẩn bị cho chứng chỉ AWS
2. **Thực hành hands-on**: Xây dựng dự án sử dụng các AWS service khác nhau
3. **Tham gia cộng đồng**: Tiếp tục tham gia sự kiện AWS Study Group
4. **Chia sẻ kiến thức**: Chia sẻ kiến thức học được với thành viên nhóm

### Chuẩn bị thi đấu
1. **Học cho chung kết**: Chuẩn bị cho vòng chung kết hoãn lại tuần sau
2. **Ôn tập điểm yếu**: Tập trung vào các chủ đề thách thức
3. **Câu hỏi thực hành**: Làm qua các câu hỏi thực hành AWS certification
4. **Chiến lược nhóm**: Thảo luận phương pháp và chiến lược với nhóm

## Tài liệu để học thêm

### Tài liệu AWS
- IAM Policies và Permissions
- Hướng dẫn Resource-Based Policies
- AWS Security Best Practices
- Cross-Account Access Patterns

### Tài liệu cộng đồng
- AWS Study Group YouTube Channel
- Nền tảng học Cloud Journey
- Cộng đồng Discord Lamentum
- Nhóm học AWS Certification

### Tài liệu thực hành
- Câu hỏi thực hành AWS Certification
- AWS Labs và tutorial hands-on
- Bài tập tình huống thực tế
- Cuộc thi quiz cộng đồng

## Hướng tới tương lai

### Chung kết tuần sau
- Chiến lược chuẩn bị cho vòng chung kết
- Ôn tập các chủ đề khó
- Phối hợp và thực hành nhóm
- Xây dựng tự tin (và có thể thêm "tâm linh"!)

### Học tập liên tục
- Tham gia thường xuyên sự kiện AWS Study Group
- Thực hành hands-on với AWS services
- Học để lấy chứng chỉ AWS
- Chia sẻ kiến thức với thành viên nhóm

## Đánh giá sự kiện

⭐⭐⭐⭐⭐ (5/5)

Thể thức thi đấu xuất sắc khiến việc học trở nên vui vẻ và hấp dẫn. Kiến thức của Anh Thịnh mang lại giá trị to lớn vượt xa các câu hỏi quiz. Các câu hỏi dựa trên tình huống thực tế rất phù hợp với việc sử dụng AWS thực tế. Cách tiếp cận "dựa vào trực giác" của các đội mang lại sự hài hước và giảm căng thẳng. Mong chờ vòng chung kết tuần sau!

## Khoảnh khắc đáng nhớ

- Các đội tranh luận câu trả lời với lập luận đam mê
- Những khoảnh khắc "aha!" tập thể khi Anh Thịnh giải thích khái niệm khó
- Phản ứng ngạc nhiên khi "dự đoán may mắn" hóa ra đúng
- Sự cạnh tranh lành mạnh và không khí ủng hộ giữa các đội
- Nhận ra rằng mọi người, bất kể trình độ, đều học hỏi cùng nhau

---

**Suy nghĩ cuối cùng:** Cuộc thi này cho thấy việc học AWS không nhất thiết phải khô khan hay nhàm chán. Kết hợp yếu tố cạnh tranh, tình huống thực tế, hướng dẫn chuyên gia và cộng đồng ủng hộ tạo ra môi trường học tập lý tưởng. Thêm nữa, một chút "tâm linh" không bao giờ có hại! 😄

