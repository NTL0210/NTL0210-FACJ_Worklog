---
title : "Mạng VPC"
date : 2026-01-01 
weight : 1 
chapter : false
pre : " <b> 5.4.1. </b> "
---

#### Tạo VPC và các subnet

1. Mở **VPC console** → **Create VPC** → **VPC and more** (tạo luôn subnet, route table và gateway).
2. Name tag `app-vpc`, IPv4 CIDR `10.0.0.0/16`.
3. **Availability Zones:** 2.
4. **Public subnet:** 2 (mỗi AZ một) — chứa ALB và NAT Gateway.
5. **Private subnet:** 2 (mỗi AZ một) — chứa các EC2 Chat & Voice.
6. **NAT Gateways:** chọn **1 mỗi AZ** (tổng hai) để có tính sẵn sàng cao.
7. **Internet Gateway:** có (tạo tự động).
8. Tạo VPC.

Sau khi tạo, kiểm tra định tuyến:
+ Route table của public subnet trỏ `0.0.0.0/0` tới **Internet Gateway**.
+ Route table của mỗi private subnet trỏ `0.0.0.0/0` tới **NAT Gateway của chính AZ đó**.

![vpc](/images/5-Workshop/5.4-VPC-Chat-Voice/5.4.1-vpc-network/vpc.png)

Sau khi tạo VPC, kiểm tra bốn subnet được chia thành public/private trên hai Availability Zone.

![subnets](/images/5-Workshop/5.4-VPC-Chat-Voice/5.4.1-vpc-network/subnets.png)

Kiểm tra route table: public subnet đi ra Internet Gateway, private subnet đi ra NAT Gateway và có route tới VPC endpoint.

![route tables](/images/5-Workshop/5.4-VPC-Chat-Voice/5.4.1-vpc-network/route-tables.png)

![internet gateway](/images/5-Workshop/5.4-VPC-Chat-Voice/5.4.1-vpc-network/internet-gateway.png)

![nat gateway](/images/5-Workshop/5.4-VPC-Chat-Voice/5.4.1-vpc-network/nat-gateways.png)

{{% notice warning %}}
Đảm bảo mỗi private subnet trỏ chiều ra tới NAT Gateway **cùng AZ**, không đi chéo AZ — vừa giữ tính sẵn sàng khi một AZ hỏng, vừa tránh phí truyền dữ liệu chéo AZ.
{{% /notice %}}
