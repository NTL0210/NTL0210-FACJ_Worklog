---
title: "Event 3: AWS Certification Quiz Competition"
date: 2026-06-20
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---

## Event Information

**Event Name:** AWS Certification Quiz Competition  
**Date & Time:** June 20, 2026  
**Format:** Team Competition (8 Teams)  
**Role:** Participant/Observer  
**Host:** AWS Study Group Community

{{< figure src="/NTL0210-FACJ_Worklog/images/123.jpg" title="AWS Certification Quiz Competition 2026" >}}

## Event Photos

{{< figure src="/NTL0210-FACJ_Worklog/images/1234.jpg" title="Competition Moment 1" >}}

{{< figure src="/NTL0210-FACJ_Worklog/images/12345.jpg" title="Competition Moment 2" >}}

## Event Overview

The AWS Certification Quiz Competition was an engaging knowledge competition where 8 teams competed against each other to answer various questions about AWS cloud services and general cloud computing concepts. The event was hosted by Anh Thịnh, who provided valuable insights and additional information about AWS concepts throughout the competition.

## Competition Format

### Round Structure

The competition progressed through multiple rounds:

**1. Qualifying Round**
- Initial screening of all 8 teams
- Questions covering fundamental AWS concepts
- Teams demonstrated varying levels of AWS knowledge

**2. Semi-Finals**
- Top teams advanced to compete
- More challenging questions on AWS services
- Increased difficulty and complexity

**3. Finals** *(Postponed)*
- Due to time constraints, the final round was postponed to next week
- Allowed teams to prepare and study more
- Built anticipation for the championship round

### Competition Dynamics

An interesting observation: teams seemed to rely on intuition and educated guessing ("playing the luck system" / "chơi hệ tâm linh") rather than purely technical knowledge, which made the competition more exciting and unpredictable!

## Key Topics Covered

### 1. AWS General Knowledge
- Core AWS services and their use cases
- Cloud computing fundamentals
- AWS best practices
- Service integrations

### 2. Practical AWS Scenarios
The questions included many practical, real-world scenarios that tested:
- Problem-solving skills
- Understanding of AWS service capabilities
- Cost optimization strategies
- Security best practices

### 3. Resource-Based vs Identity-Based Permissions

**Highlight Topic:** One of the most interesting and practical questions focused on **preferring resource-based policies over identity-based policies**.

#### Why Resource-Based Policies?

**Resource-Based Policies Advantages:**
- **Cross-Account Access**: Easier to grant access to resources from other AWS accounts
- **Centralized Control**: Permissions defined directly on the resource
- **Simplified Management**: Don't need to modify user/role policies
- **Explicit Control**: Clear visibility of who can access the resource
- **No Role Assumption Required**: Direct access without assuming roles

**Common Use Cases:**
- **S3 Bucket Policies**: Control access to buckets and objects
- **SQS Queue Policies**: Manage message queue permissions
- **SNS Topic Policies**: Configure topic access
- **Lambda Function Policies**: Control function invocation permissions
- **KMS Key Policies**: Manage encryption key access

**Example Scenario:**
When you want to allow multiple accounts or services to access an S3 bucket, it's more efficient to:
- ✅ Add a bucket policy (resource-based) allowing those accounts
- ❌ Rather than modifying IAM policies (identity-based) in each account

#### Identity-Based Policies vs Resource-Based Policies

**Identity-Based Policies:**
- Attached to IAM users, groups, or roles
- Control what the identity can do
- Must be managed in each account

**Resource-Based Policies:**
- Attached directly to resources (S3, SQS, SNS, etc.)
- Control who can access the resource
- Managed centrally on the resource

**Best Practice:** Use resource-based policies when you need to grant cross-account access or want centralized permission management.

## Insights from Anh Thịnh

Throughout the competition, Anh Thịnh provided valuable additional information and context for each question:

### Technical Insights
- **Real-world applications**: How the concepts apply in production environments
- **Common mistakes**: Pitfalls to avoid when implementing AWS services
- **Best practices**: Industry-standard approaches to AWS architecture
- **Cost considerations**: How different solutions impact AWS costs
- **Security implications**: Security aspects of various AWS configurations

### Learning Approach
- **Understanding over memorization**: Focus on concepts rather than rote learning
- **Practical experience**: Importance of hands-on practice with AWS services
- **Scenario-based thinking**: Applying knowledge to real-world problems
- **Cost optimization**: Always consider cost-effectiveness in solutions

## Key Learnings

### 1. AWS Permissions Management
- Deep understanding of resource-based vs identity-based policies
- When to use each type of policy
- Cross-account access patterns
- Security best practices for permissions

### 2. Practical AWS Knowledge
- Real-world scenarios are more valuable than theoretical knowledge
- Understanding service interactions is crucial
- Cost and security must be considered in every decision

### 3. Team Competition Skills
- Quick thinking under pressure
- Collaborative problem-solving
- Strategic guessing when uncertain (the "intuition system"!)
- Learning from other teams' approaches

### 4. Continuous Learning
- AWS is vast and constantly evolving
- Community events provide valuable learning opportunities
- Practical questions reveal knowledge gaps
- Certification-level questions test deep understanding

## Personal Reflections

### Technical Growth
This competition highlighted several areas for improvement:
- **Permissions Architecture**: Need deeper understanding of IAM and resource policies
- **Service Integration**: How different AWS services work together
- **Best Practices**: Industry-standard approaches to common problems
- **Practical Application**: Translating theoretical knowledge to real scenarios

### Competitive Experience
- **Pressure Testing**: Answering questions under time pressure reveals true understanding
- **Knowledge Gaps**: Competition exposed areas needing more study
- **Learning from Others**: Observed different problem-solving approaches from other teams
- **Fun Factor**: The "intuition-based" approach made the competition entertaining and less stressful!

### Community Value
- **Expert Guidance**: Anh Thịnh's additional explanations were extremely valuable
- **Peer Learning**: Learning from other teams' answers and reasoning
- **Networking**: Connected with other AWS enthusiasts
- **Motivation**: Competition format motivated deeper AWS learning

## Interesting Observations

### The "Intuition System" (Hệ Tâm Linh)
Teams appeared to rely heavily on educated guessing and intuition:
- **Pattern Recognition**: Recognizing common AWS patterns
- **Elimination Strategy**: Ruling out obviously wrong answers
- **Lucky Guesses**: Sometimes getting answers right without full confidence
- **Team Chemistry**: Groups that worked well together seemed luckier!

This approach, while humorous, actually demonstrates:
- Strong foundational understanding allows good intuition
- AWS experience builds pattern recognition
- Confidence in educated guessing is a valuable skill
- Teamwork can compensate for individual knowledge gaps

## Action Items

### Immediate Study Focus
1. **Master IAM Policies**: Deep dive into resource-based vs identity-based policies
2. **Cross-Account Access**: Study cross-account access patterns and best practices
3. **Service Integration**: Understand how different AWS services interact
4. **Practical Scenarios**: Practice with real-world AWS architecture problems

### Long-term Development
1. **AWS Certification**: Consider preparing for AWS certifications
2. **Hands-on Practice**: Build projects using various AWS services
3. **Community Engagement**: Continue participating in AWS Study Group events
4. **Knowledge Sharing**: Share learnings with team members

### Competition Preparation
1. **Study for Finals**: Prepare for the postponed final round next week
2. **Review Weak Areas**: Focus on topics that were challenging
3. **Practice Questions**: Work through AWS certification practice questions
4. **Team Strategy**: Discuss approach and strategies with team

## Resources for Further Learning

### AWS Documentation
- IAM Policies and Permissions
- Resource-Based Policies Guide
- AWS Security Best Practices
- Cross-Account Access Patterns

### Community Resources
- AWS Study Group YouTube Channel
- Cloud Journey Learning Platform
- Discord Lamentum Community
- AWS Certification Study Groups

### Practice Materials
- AWS Certification practice questions
- AWS Labs and hands-on tutorials
- Real-world scenario exercises
- Community quiz competitions

## Looking Forward

### Finals Next Week
- Preparation strategy for final round
- Review of challenging topics
- Team coordination and practice
- Confidence building (and maybe more "intuition"!)

### Continued Learning
- Regular participation in AWS Study Group events
- Hands-on practice with AWS services
- Studying for AWS certifications
- Sharing knowledge with team members

## Event Rating

⭐⭐⭐⭐⭐ (5/5)

Excellent competitive format that made learning fun and engaging. Anh Thịnh's insights added tremendous value beyond just the quiz questions. The practical, scenario-based questions were highly relevant to real-world AWS usage. The "intuition-based" approach by teams added humor and reduced stress. Looking forward to the finals next week!

## Memorable Moments

- Teams debating answers with passionate arguments
- The collective "aha!" moments when Anh Thịnh explained tricky concepts
- Surprised reactions when "lucky guesses" turned out correct
- The friendly rivalry and supportive atmosphere among teams
- Realization that everyone, regardless of level, was learning together

---

**Final Thoughts:** This competition demonstrated that learning AWS doesn't have to be dry or boring. Combining competitive elements, practical scenarios, expert guidance, and a supportive community creates an ideal learning environment. Plus, a little "intuition" never hurts! 😄

