---
title : "Pipeline xử lý AI"
date : 2026-01-01 
weight : 5 
chapter : false
pre : " <b> 5.5. </b> "
---

#### Từ bundle họp đến công việc được giao

Khi một phiên họp có transcript/audio mới, dữ liệu được lưu thành **bundle trong S3**. Việc upload này kích hoạt pipeline hướng sự kiện: gọi AI bên ngoài để tóm tắt nội dung và tách việc, lưu task vào DynamoDB, rồi hiển thị hoặc thông báo cho người được giao.

1. [Bundle S3 & trigger EventBridge](5.5.1-s3-eventbridge/)
2. [Step Functions & các Lambda AI](5.5.2-stepfunctions-ai/)
3. [Lưu công việc & thông báo bằng SNS](5.5.3-notify-sns/)
