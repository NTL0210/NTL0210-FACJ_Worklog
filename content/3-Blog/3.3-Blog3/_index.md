---
title: "AWS Cost Estimation with AWS Pricing Calculator"
date: 2025-01-15
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# AWS Cost Estimation with AWS Pricing Calculator

{{< figure src="/NTL0210-FACJ_Worklog/images/blog3.png" title="AWS Pricing Calculator" >}}

Hello AWS Study Group VN!

During my AWS learning and practice journey, I've noticed one question that comes up very frequently:

> **"How much will deploying a service cost per month?"**

Instead of creating real resources and waiting for the end-of-month bill, AWS provides a free tool to help us estimate costs before deployment: **AWS Pricing Calculator**.

In this post, I'll share how to use AWS Pricing Calculator to calculate costs for a basic EC2 instance.

---

## 1. WHAT IS AWS PRICING CALCULATOR?

**AWS Pricing Calculator** is a tool that allows you to create scenarios for new workloads or modify existing workloads to estimate costs.

### Features:

✅ **Completely free**: No charge for using the tool
✅ **No AWS login required**: Can be used directly in browser
✅ **Supports multiple services**: EC2, S3, RDS, Lambda, and hundreds of other AWS services
✅ **Share estimates**: Can export or share links with team

---

## 2. WHY USE AWS PRICING CALCULATOR?

In my opinion, this tool is useful in the following situations:

### Cost Planning

- Estimate costs before deploying systems
- Plan project budgets
- Compare costs between different options

### Share with Team

- Share estimates with clients or team members
- Export to PDF or CSV for reports
- Save scenarios for future reference

### Cost Optimization

- Compare different instance types
- Evaluate impact of Reserved Instances or Savings Plans
- Calculate costs when scaling up/down

---

## 3. AWS PRICING CALCULATOR WORKFLOW

According to AWS documentation, the deployment process can be summarized as follows:

### Step 1: Access AWS Pricing Calculator

Visit: [https://calculator.aws](https://calculator.aws)

No AWS Account login required.

### Step 2: Create Estimate

Click **"Create estimate"** to start.

### Step 3: Select service to estimate

- Search for service (e.g., EC2, S3, RDS)
- Click **"Configure"** on the service you want to add

### Step 4: Enter parameters

For EC2, required parameters include:

#### Location (Region)
- Select AWS Region (e.g., Asia Pacific - Singapore)
- Costs vary between regions

#### Instance Type
- Family: General Purpose, Compute Optimized, Memory Optimized...
- Size: t3.micro, t3.small, t3.medium...
- vCPUs and Memory

#### Operating System
- Linux, Windows, RHEL, SUSE...
- Windows is typically more expensive than Linux

#### Workload
- **On-Demand**: Pay per hour, flexible
- **Reserved Instances**: 1-3 year commitment, save 30-70%
- **Savings Plans**: More flexible than RI, save up to 72%
- **Spot Instances**: Lowest price but can be interrupted

#### Utilization
- Hours running per day
- Days running per month

#### Storage
- EBS Volume Type: gp3, gp2, io2, st1...
- Storage Amount (GB)
- IOPS and Throughput (if needed)

#### Data Transfer
- Data Transfer Out to Internet
- Data Transfer between Regions
- Elastic IP (if used)

### Step 5: Review Estimate

After entering all parameters, AWS Pricing Calculator will display:

- **Monthly cost**: Estimated monthly cost
- **12 months total**: Total cost for 12 months
- **Breakdown by service**: Details by component

### Step 6: Add other services (if needed)

Can continue adding other services like:
- S3 for storage
- RDS for database
- CloudFront for CDN
- Lambda for serverless functions

### Step 7: Save and Share

- **Save estimate**: Create shareable link
- **Export to PDF**: Export PDF report
- **Export to CSV**: Export Excel data

---

## 4. REAL EXAMPLE: CALCULATING EC2 COST

Suppose I want to deploy a Web Server with the following configuration:

### Configuration:

- **Region**: Asia Pacific (Singapore)
- **Instance Type**: t3.medium (2 vCPUs, 4 GB RAM)
- **OS**: Amazon Linux (free)
- **Workload**: On-Demand
- **Usage**: 24 hours/day, 30 days/month
- **Storage**: 30 GB gp3
- **Data Transfer Out**: 100 GB/month

### Estimated result:

```
EC2 Instance Cost:          $30.40/month
EBS Storage (30 GB gp3):    $2.40/month
Data Transfer Out (100 GB): $9.00/month
─────────────────────────────────────────
TOTAL:                      $41.80/month
```

### Comparison with Reserved Instance (1 year):

```
EC2 Instance Cost:          $20.00/month (-34%)
EBS Storage (30 GB gp3):    $2.40/month
Data Transfer Out (100 GB): $9.00/month
─────────────────────────────────────────
TOTAL:                      $31.40/month
SAVINGS:                    $10.40/month ($124.80/year)
```

---

## 5. IMPORTANT NOTES

### AWS Pricing Calculator only provides cost estimates

Actual costs may vary depending on:

⚠️ **Actual usage**: If you use more than expected
⚠️ **Generated network traffic**: Data transfer can spike unexpectedly
⚠️ **Additional services**: Backup, Snapshot, Elastic IP...
⚠️ **Service price changes by Region**: AWS may adjust pricing

### Does not include Free Tier

AWS Pricing Calculator **DOES NOT automatically apply Free Tier**. If you're using Free Tier, actual costs will be lower than the estimate.

**AWS Free Tier for EC2:**
- 750 hours/month t2.micro or t3.micro
- 30 GB EBS Storage
- 15 GB Data Transfer Out

### Hidden costs to note

- **Unused Elastic IP**: $0.005/hour
- **EBS Snapshots**: Charged per GB-month
- **Data Transfer between AZs**: $0.01/GB
- **CloudWatch Logs**: May incur charges

---

## 6. USAGE TIPS

### Tip 1: Start with On-Demand then optimize later

- Use On-Demand in the initial phase
- Monitor usage pattern for 1-2 months
- Switch to Reserved Instances or Savings Plans later

### Tip 2: Compare multiple scenarios

- Create multiple estimates with different configurations
- Compare cost and performance
- Choose option that fits budget

### Tip 3: Use Tags to group services

- Name each service clearly in estimate
- Group by environment: Dev, Staging, Production
- Easy to track and compare

### Tip 4: Export and store estimates

- Save estimates for each project
- Export PDF to attach to proposals
- Compare with actual bills to adjust

---

## 7. CONCLUSION

In my opinion, **AWS Pricing Calculator** is a useful tool for anyone learning or working with AWS.

### Main benefits:

✅ Estimate costs before deployment
✅ Compare different options
✅ Detailed budget planning
✅ Share with team and clients
✅ Optimize AWS costs

Instead of deploying resources and then checking costs, we can proactively estimate budgets in advance, compare options, and make appropriate decisions right from the system design phase.

If you've used AWS Pricing Calculator or have experience optimizing AWS costs, feel free to share in the comments.

---

## REFERENCES

* [AWS Pricing Calculator](https://calculator.aws)
* [AWS Pricing Calculator Documentation](https://aws.amazon.com/premiumsupport/knowledge-center/estimate-aws-monthly-billing-cost/)
* [AWS Free Tier](https://aws.amazon.com/free/)
* [AWS Cost Management](https://aws.amazon.com/aws-cost-management/)

---

## Related Posts

- [KIRO – AWS AI Coding Assistant and AI-Assisted Development Lifecycle](../3.1-blog1/)
- [Running Traditional Web Applications on AWS Nitro Enclaves](../3.2-blog2/)

---

*Written during my internship at FCJ (April 17 - July 10, 2026) and shared with AWS Study Group VN.*
