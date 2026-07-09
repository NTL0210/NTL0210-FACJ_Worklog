---
title : "VPC Network"
date : 2026-01-01 
weight : 1 
chapter : false
pre : " <b> 5.4.1. </b> "
---

#### Create the VPC and subnets

1. Open the **VPC console** → **Create VPC** → **VPC and more** (this creates subnets, route tables and gateways together).
2. Name tag `app-vpc`, IPv4 CIDR `10.0.0.0/16`.
3. **Availability Zones:** 2.
4. **Public subnets:** 2 (one per AZ) — these hold the ALB and the NAT Gateways.
5. **Private subnets:** 2 (one per AZ) — these hold the EC2 Chat & Voice instances.
6. **NAT Gateways:** choose **1 per AZ** (two total) for high availability.
7. **Internet Gateway:** yes (created automatically).
8. Create the VPC.

After creation, verify the routing:
+ Public subnet route tables send `0.0.0.0/0` to the **Internet Gateway**.
+ Each private subnet route table sends `0.0.0.0/0` to the **NAT Gateway in its own AZ**.

![vpc](/images/5-Workshop/5.4-VPC-Chat-Voice/5.4.1-vpc-network/vpc.png)

After creating the VPC, verify the four subnets split into public/private subnets across two Availability Zones.

![subnets](/images/5-Workshop/5.4-VPC-Chat-Voice/5.4.1-vpc-network/subnets.png)

Check the route tables: public subnets route to the Internet Gateway, while private subnets route to the NAT Gateway and VPC endpoints.

![route tables](/images/5-Workshop/5.4-VPC-Chat-Voice/5.4.1-vpc-network/route-tables.png)

![internet gateway](/images/5-Workshop/5.4-VPC-Chat-Voice/5.4.1-vpc-network/internet-gateway.png)

![nat gateway](/images/5-Workshop/5.4-VPC-Chat-Voice/5.4.1-vpc-network/nat-gateways.png)

{{% notice warning %}}
Make sure each private subnet routes egress to the NAT Gateway **in the same AZ**, not across AZs — this preserves availability if one AZ fails and avoids cross-AZ data charges.
{{% /notice %}}
