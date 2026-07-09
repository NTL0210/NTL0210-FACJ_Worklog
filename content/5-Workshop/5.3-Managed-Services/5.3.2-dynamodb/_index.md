---
title : "Amazon DynamoDB"
date : 2026-01-01 
weight : 2 
chapter : false
pre : " <b> 5.3.2. </b> "
---

#### Create the DynamoDB table

The project uses one main DynamoDB table, `ai-meeting-platform`, with a **single-table design**. Instead of creating many separate relational-style tables, data types such as users, meetings, tasks, notifications, and processing status are separated by keys and entity attributes.

This design fits DynamoDB because DynamoDB is usually optimized around application **access patterns**. For the MVP scope, one main table reduces the number of deployed resources, simplifies IAM permissions, and still supports the core queries of the platform.

1. Open the **DynamoDB console** → **Create table**.
2. Create the table `ai-meeting-platform`.
3. Select **On-demand** capacity mode for the demo/MVP phase.
4. Keep encryption at rest with the default settings.

![dynamodb table](/images/5-Workshop/5.3-Managed-Services/5.3.2-dynamodb/tables.png)

After the backend or AI pipeline runs, open **Explore table items** to verify that meeting/task data has been written.

![dynamodb task item](/images/5-Workshop/5.3-Managed-Services/5.3.2-dynamodb/task-item.png)

{{% notice note %}}
Single-table design is a common DynamoDB pattern. As the system grows, GSIs or additional tables can be added if access patterns change.
{{% /notice %}}
