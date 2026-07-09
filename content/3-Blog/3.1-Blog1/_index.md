---
title: "Building Serverless Applications with AWS Lambda and DynamoDB"
date: 2026-07-08
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Building Serverless Applications with AWS Lambda and DynamoDB

{{< figure src="/NTL0210-FACJ_Worklog/images/blog1.png" title="AWS Serverless Architecture Overview" >}}

## Introduction

During my 12-week internship at FCJ, I built a production-ready serverless application using AWS Lambda and DynamoDB. This blog post shares the architectural decisions, design patterns, and best practices I learned while implementing a multi-tenant AI Meeting Workforce Platform.

## Why Serverless?

Serverless architecture offers several compelling benefits for modern applications:

- **Zero server management**: Focus on code, not infrastructure
- **Automatic scaling**: Handle traffic spikes without manual intervention
- **Pay-per-use pricing**: Only pay for actual compute time and storage
- **High availability**: Built-in redundancy across multiple availability zones
- **Faster time to market**: Deploy features quickly without infrastructure setup

For an internship project with unpredictable usage patterns and limited budget, serverless was the perfect choice.

## Architecture Overview

The platform uses a fully serverless architecture:

```
Frontend (Next.js on CloudFront)
        ↓
API Gateway (REST API)
        ↓
AWS Lambda Functions (Express.js)
        ↓
DynamoDB (Single-table design)
        ↓
S3 (Audio recordings & transcripts)
```

### Key Components:

1. **AWS Lambda**: Runs backend API logic
2. **Amazon DynamoDB**: Stores all application data
3. **Amazon S3**: Stores meeting recordings and transcripts
4. **API Gateway**: Exposes REST endpoints
5. **AWS Cognito**: Handles user authentication

## DynamoDB Single-Table Design

One of the most important architectural decisions was using a **single-table design** for DynamoDB. Instead of creating separate tables for each entity (users, workspaces, meetings, tasks), everything lives in one table.

### Why Single-Table?


- **Better performance**: Fetch related data in a single query
- **Lower cost**: Fewer read/write operations
- **Simplified access patterns**: All data organized by workspace
- **Easier multi-tenancy**: Natural isolation between workspaces

### Table Structure:

```
PK                    SK                      Attributes
---------------------------------------------------------
WORKSPACE#123         METADATA#               name, createdAt, ...
WORKSPACE#123         USER#alice              role, joinedAt, ...
WORKSPACE#123         USER#bob                role, joinedAt, ...
WORKSPACE#123         MEETING#abc             title, date, ...
WORKSPACE#123         TASK#xyz                title, assignee, ...
```

### Global Secondary Indexes (GSI):

**GSI1**: For time-based queries
- PK: `WORKSPACE#123`
- SK: `TIMESTAMP#2026-07-08`
- Use case: "Get all meetings in workspace ordered by date"

**GSI2**: For user-specific queries
- PK: `WORKSPACE#123`
- SK: `ASSIGNEE#alice`
- Use case: "Get all tasks assigned to a user"

## Lambda Function Design

Each Lambda function follows a consistent pattern:

### Function Structure:
```javascript
// handler.js
exports.handler = async (event) => {
  try {
    // 1. Extract and validate input
    const { workspaceId, userId } = event;
    
    // 2. Perform business logic
    const result = await processRequest(workspaceId, userId);
    
    // 3. Return response
    return {
      statusCode: 200,
      body: JSON.stringify(result)
    };
  } catch (error) {
    // 4. Handle errors gracefully
    console.error(error);
    return {
      statusCode: 500,
      body: JSON.stringify({ error: error.message })
    };
  }
};
```

### Best Practices Applied:

1. **Keep functions small**: Each function does one thing well
2. **Use environment variables**: Store configuration separately
3. **Optimize cold starts**: Minimize dependencies and initialization code
4. **Implement proper error handling**: Log errors and return meaningful messages
5. **Set appropriate timeouts**: Balance between reliability and cost

## Multi-Tenancy Implementation

Supporting multiple workspaces (tenants) requires careful design:

### Workspace Isolation:

- Every DynamoDB query includes `workspaceId` in the partition key
- S3 objects stored with workspace prefix: `workspace-123/meeting-abc/audio.mp3`
- Lambda functions validate user has access to requested workspace
- Cognito user pools with custom attributes for workspace membership

### Access Control:
```javascript
async function validateAccess(userId, workspaceId) {
  const membership = await dynamodb.get({
    TableName: 'ai-meeting-workforce',
    Key: {
      PK: `WORKSPACE#${workspaceId}`,
      SK: `USER#${userId}`
    }
  });
  
  if (!membership) {
    throw new Error('Access denied');
  }
  
  return membership.role; // 'admin', 'member', 'guest'
}
```

## Cost Optimization

Keeping costs low was critical for an internship project:

### Strategies Implemented:

1. **DynamoDB On-Demand Mode**: Pay only for actual requests
2. **Lambda Memory Optimization**: Found sweet spot at 512 MB
3. **S3 Lifecycle Policies**: Move old recordings to cheaper storage tiers
4. **CloudFront Caching**: Reduce Lambda invocations for static content
5. **Connection Pooling**: Reuse DynamoDB connections across Lambda invocations

### Actual Monthly Costs:
- Lambda: $2-5
- DynamoDB: $5-12
- S3: $3-8
- **Total: ~$44/month** for development environment

## Challenges & Solutions

### Challenge 1: Cold Starts
**Problem**: First request after idle period took 2-3 seconds  
**Solution**: 
- Reduced Lambda package size
- Used AWS SDK v3 (smaller than v2)
- Implemented connection pooling
- Result: Cold starts reduced to < 1 second

### Challenge 2: DynamoDB Hot Partitions
**Problem**: High traffic to specific workspaces caused throttling  
**Solution**:
- Switched from provisioned to on-demand capacity
- Implemented caching layer for frequent reads
- Added GSIs to distribute query load

### Challenge 3: Multi-Tenant Data Isolation
**Problem**: Risk of data leaks between workspaces  
**Solution**:
- Strict validation in every Lambda function
- Workspace ID in every DynamoDB key
- Automated tests for access control
- Code reviews focused on security

## Key Learnings

1. **Start with single-table design**: Easier to scale than multiple tables
2. **Monitor everything**: CloudWatch metrics are your friend
3. **Optimize for cost early**: Small changes add up
4. **Test multi-tenancy thoroughly**: Security is critical
5. **Use AWS best practices**: They exist for good reasons

## Conclusion

Building a serverless application with AWS Lambda and DynamoDB taught me valuable lessons about cloud architecture, cost optimization, and production-ready development. The platform handles real-time communication, AI processing, and multi-tenant data isolation—all while staying within a student-friendly budget.

For anyone starting with serverless on AWS, my advice is: start small, monitor closely, and iterate based on real usage patterns. The serverless model removes infrastructure complexity, letting you focus on solving actual problems.

## Resources

- [AWS Lambda Best Practices](https://docs.aws.amazon.com/lambda/latest/dg/best-practices.html)
- [DynamoDB Single-Table Design](https://aws.amazon.com/blogs/compute/creating-a-single-table-design-with-amazon-dynamodb/)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [Project GitHub Repository](https://github.com/NTL0210/FACJ_Worklog)

---

**Author:** Nguyễn Tấn Lộc  
**Date:** July 8, 2026  
**Tags:** AWS, Lambda, DynamoDB, Serverless, Multi-tenancy, Cloud Architecture
