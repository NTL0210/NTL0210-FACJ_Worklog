---
title: "Proposal"
date: 2026-04-17
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# AI MEETING WORKFORCE PLATFORM
## An AWS Serverless Solution for Transforming Meetings into Measurable Execution

---

### 1. Executive Summary

The AI Meeting Workforce Platform is a comprehensive solution designed to transform meeting discussions into measurable execution outcomes. Built during my 12-week internship at FCJ (April - July 2026), this platform demonstrates modern AWS Serverless architecture implementation with real-time voice communication, AI-powered meeting analysis, and workspace collaboration features.

**Project Duration:** April 17, 2026 - July 10, 2026 (12 weeks)

**Technology Stack:**
- **Frontend:** Next.js 16, React 19, TailwindCSS
- **Backend:** AWS Lambda, API Gateway, Express.js
- **Database:** Amazon DynamoDB with single-table design
- **Storage:** Amazon S3 with lifecycle policies
- **Authentication:** AWS Cognito
- **AI Processing:**  Google Gemini/OpenAI
- **Real-time:** WebRTC, Socket.io, Custom signaling server
- **Deployment:**  CloudFront

**Key Features Implemented:**
- Multi-tenant workspace management with unlimited teams
- Real-time voice communication (Discord-like experience)
- Text chat with message history
- Meeting recording and transcription
- AI-powered meeting analysis and task extraction
- Workspace member invitation system
- Role-based access control

---

### 2. Problem Statement

#### What's the Problem?

Modern teams conduct countless meetings but struggle with execution:
- **Lost Action Items**: Meeting decisions scattered across emails, chat, and notes
- **No Follow-Through**: Team members forget commitments made during discussions
- **Time Waste**: Hours spent manually summarizing meetings and creating tasks
- **Zero Visibility**: No centralized way to track decisions and responsibilities
- **Accountability Gap**: Difficulty tracking progress on meeting commitments

#### The Solution

The platform uses **AWS Serverless Architecture** to automate the meeting-to-execution workflow:

1. **Meeting Recording & Transcription**: Browser-based recording with AWS Transcribe
2. **AI Analysis**: Google Gemini/OpenAI processes transcripts to generate summaries
3. **Automatic Task Creation**: AI-identified action items become trackable tasks
4. **Real-time Voice**: WebRTC-based voice communication for seamless collaboration
5. **Workspace Management**: Multi-tenant architecture with team and channel management
6. **Execution Tracking**: Real-time dashboard showing task progress and completion

**Key Differentiators:**
- Automatic task creation and assignment
- Integrated workspace for team collaboration
- Real-time voice communication built-in
- Focus on measuring execution outcomes

---

### 3. Solution Architecture

The platform employs AWS Serverless Architecture with multi-tenancy support and workspace isolation.



**High-Level Architecture:**

![High-Level Architecture](/NTL0210-FACJ_Worklog/images/arch1.png)

#### AWS Services Used

• **AWS Lambda**: Serverless compute for API logic and AI processing
• **Amazon DynamoDB**: NoSQL database with on-demand billing
• **Amazon S3**: Object storage for audio recordings and transcripts
• **AWS Step Functions**: Orchestrates AI processing pipeline
• **Amazon API Gateway**: REST API endpoints
• **AWS Cognito**: User authentication with JWT tokens
• **AWS Amplify & CloudFront**: Static site hosting and CDN
• **AWS Transcribe**: Automatic speech-to-text conversion
• **Amazon CloudWatch**: Logging and monitoring
• **AWS IAM**: Fine-grained access control

---

### 4. Technical Implementation

#### Implementation Timeline (Actual Progress)



**Week 1-2 (April 17-26): Onboarding & AWS Fundamentals**
- Completed AWS Study Group onboarding process
- Studied AWS Module 1 (Cloud Concepts) and Module 2 (Security & Compliance)
- Set up AWS account and IAM configuration
- Learned foundational cloud architecture patterns

**Week 3-4 (April 27 - May 10): AWS Core Services**
- Completed Module 3 (Technology & Services) and Module 4 labs
- Hands-on practice with EC2, VPC, S3, and RDS
- Attended AWS Study Group Workshop (May 9) - AI in Development
- Encountered and debugged lab errors (learning experience)

**Week 5-6 (May 11-24): Advanced AWS & Community Engagement**
- Completed Module 5 and started Module 6
- Worked on personal AWS projects applying learned concepts
- Attended FCAJ Community Day (May 23) with 6 expert speakers
- Learned about AI context importance and CloudFront advanced features

**Week 7 (May 25 - June 7): Project Planning & Module 7**
- Completed Module 7 (advanced AWS concepts)
- Participated in group project work (3 days)
- Brainstormed and planned final project architecture
- Created initial project proposal

**Week 8 (June 8-14): Architecture Design & Development Start**
- Began architecture design revisions
- Started implementing core project features
- Set up development environment
- Initiated AWS services integration planning

**Week 9 (June 15-21): AWS Integration Challenges**
- Continued project development
- Attempted AWS services integration
- Encountered connection challenges (learning experience)
- Attended AWS Certification Quiz Competition (June 20)
- Learned about resource-based vs identity-based policies

**Week 10 (June 22-28): Feature Implementation**
- Developed core project features:
  - User authentication system
  - Real-time communication features
  - UI/UX improvements
  - Backend API integration
- Attended FCAJ Community Day (June 27) - AI Agents & DevOps
- Learned from 9 speakers about AgenticOps, Voice AI, AWS DevOps Agent, Amazon Q, and MCP

**Week 11 (June 29 - July 5): CloudFront Deployment & Debugging**
- Deployed application to CloudFront for testing
- Encountered and fixed multiple deployment issues:
  - **Login errors**: CORS configuration, cookie domain problems
  - **Voice chat errors**: WebRTC signaling, STUN/TURN configuration
  - **Text chat errors**: WebSocket connection stability
  - **Workspace invitation errors**: Email service integration issues
- Learned production debugging and root cause analysis

**Week 12 (July 6-10): Final Testing & Documentation**
- Comprehensive testing and validation
- Production deployment finalization
- Documentation completion
- Internship wrap-up and presentation

#### Key Technical Challenges & Solutions

**Challenge 1: AWS Services Integration**
- **Problem**: Initial attempts to connect AWS services faced connectivity issues
- **Solution**: Researched AWS best practices, configured VPC endpoints, implemented proper IAM policies
- **Learning**: Understanding AWS networking and security models

**Challenge 2: CloudFront Deployment Errors**
- **Problem**: Multiple errors when deploying to CloudFront (login, voice, chat, invitations)
- **Root Causes**:
  - CORS headers not configured for CDN
  - Cookie security flags incompatible with CloudFront
  - WebRTC signaling server accessibility through CDN
  - WebSocket connection instability
- **Solutions**:
  - Updated CORS configuration for CloudFront distribution
  - Configured appropriate cookie settings (SameSite, Secure, Domain)
  - Reconfigured WebRTC signaling and STUN/TURN servers
  - Implemented WebSocket reconnection logic
- **Learning**: Production environment debugging, CDN architecture, real-time communication challenges

**Challenge 3: Real-time Voice Communication**
- **Problem**: Implementing WebRTC for voice chat with low latency
- **Solution**: Built custom signaling server, implemented peer-to-peer connections, optimized audio codecs
- **Learning**: WebRTC architecture, NAT traversal, media streaming



#### Technology Stack

**Frontend:**
- Next.js 16.2.9 with React 19
- TailwindCSS for responsive design
- Socket.io Client for real-time features
- WebRTC for voice communication
- AWS Amplify SDK for authentication

**Backend:**
- Express.js on AWS Lambda
- TypeScript with strict mode
- AWS SDK v3 (DynamoDB, S3, Cognito)
- Custom WebRTC signaling server
- Step Functions for AI pipeline

**Database & Storage:**
- DynamoDB single-table design with GSIs
- S3 for audio files and transcripts
- On-demand billing for cost optimization

**AI Processing:**
- AWS Transcribe for speech-to-text
- Google Gemini / OpenAI for analysis
- Lambda functions for processing
- Step Functions for orchestration

**Deployment:**
- AWS Amplify for frontend hosting
- CloudFront CDN for global distribution
- Lambda for serverless backend
- GitHub Actions for CI/CD

---

### 5. Timeline & Milestones

#### Actual Project Timeline

| Week | Dates | Focus Area | Key Achievements |
|------|-------|------------|------------------|
| 1-2 | Apr 17-26 | Onboarding | AWS fundamentals, Module 1-2, account setup |
| 3-4 | Apr 27 - May 10 | Core Services | Module 3-4, EC2/VPC/S3 labs, AWS Workshop |
| 5-6 | May 11-24 | Advanced Concepts | Module 5-6, personal projects, FCAJ Community Day |
| 7 | May 25 - Jun 7 | Project Planning | Module 7, group project, proposal creation |
| 8 | Jun 8-14 | Architecture | Design revision, development start |
| 9 | Jun 15-21 | Integration | AWS integration attempts, Quiz Competition |
| 10 | Jun 22-28 | Implementation | Auth, voice, UI features, FCAJ Day (AI/DevOps) |
| 11 | Jun 29 - Jul 5 | Deployment | CloudFront deployment, bug fixes |
| 12 | Jul 6-10 | Finalization | Testing, documentation, presentation |

#### Milestone Achievements

**✅ Month 1 Milestones (April 17 - May 17):**
- Completed AWS Modules 1-4 with hands-on labs
- Attended AWS Study Group Workshop
- Set up AWS development environment
- Gained foundational cloud knowledge

**✅ Month 2 Milestones (May 18 - June 17):**
- Completed AWS Modules 5-7
- Attended FCAJ Community Day (May 23)
- Completed group project work
- Started final project development
- Attended AWS Certification Quiz (June 20)

**✅ Month 3 Milestones (June 18 - July 10):**
- Attended FCAJ Community Day (June 27) - AI/DevOps
- Implemented core features (auth, voice, chat, workspace)
- Deployed to CloudFront
- Fixed production deployment issues
- Completed comprehensive testing
- Finalized documentation

---

### 6. Budget Estimation

#### Infrastructure Costs (Actual Monthly - Development Phase)

**Compute:**
- **AWS Lambda**: $2-5/month
  - Estimated 50,000-100,000 requests/month
  - 512 MB memory average
  - Free tier covered significant usage

**Storage:**
- **Amazon S3**: $3-8/month
  - Standard storage for audio/transcripts
  - Lifecycle policy to cheaper tiers
  - ~5,000 requests/month

- **Amazon DynamoDB**: $5-12/month
  - On-demand billing mode
  - Estimated 30,000 read/write units
  - 3-5 GB storage

**AI Processing:**
- **AWS Transcribe**: $8-15/month
  - Limited usage during testing
  - $0.024 per minute for standard

- **LLM API (Gemini/OpenAI)**: $5-10/month
  - Rate-limited for cost control
  - Cached results in DynamoDB

**Networking:**
- **CloudFront**: $2-5/month
  - Static asset distribution
  - Data transfer costs

- **API Gateway**: $1-2/month
  - REST API requests
  - Within free tier initially

**Other Services:**
- **AWS Amplify**: $0-1/month (free tier)
- **AWS Cognito**: $0/month (< 50,000 users free)
- **CloudWatch Logs**: $1-3/month (optimized)

**Total Monthly Cost: $27-61 USD**
**Average: ~$44/month during development**
**Total 3-Month Cost: ~$132 USD**

#### Cost Optimization Strategies Implemented

1. **On-Demand Pricing**: Used DynamoDB on-demand mode for unpredictable workloads
2. **Lambda Optimization**: Optimized function memory and timeout settings
3. **S3 Lifecycle Policies**: Automated transition to cheaper storage tiers
4. **CloudFront Caching**: Reduced origin requests through proper caching
5. **Free Tier Utilization**: Maximized AWS free tier benefits during development
6. **Resource Tagging**: Tracked costs by feature and environment

---

### 7. Risk Assessment & Mitigation

#### Technical Risks

**Risk 1: CloudFront Deployment Issues (HIGH - Occurred)**
- **Impact**: Production deployment failures affecting all users
- **Probability**: HIGH (This actually happened in Week 11)
- **Mitigation Implemented**:
  - Thorough testing in staging environment before production
  - Documented all CloudFront-specific configurations
  - Created rollback procedures
  - Implemented comprehensive error logging
- **Lessons Learned**: Local testing insufficient for CDN deployments

**Risk 2: Real-time Communication Failures (MEDIUM)**
- **Impact**: Voice chat and text messaging not working reliably
- **Probability**: MEDIUM
- **Mitigation**:
  - Implemented fallback mechanisms for WebRTC
  - Added connection retry logic for WebSockets
  - Set up monitoring for real-time features
  - Created alternative communication channels
- **Status**: Mitigated through robust error handling

**Risk 3: AWS Service Limits (LOW-MEDIUM)**
- **Impact**: Application throttling or service unavailability
- **Probability**: LOW
- **Mitigation**:
  - Monitored service quotas in CloudWatch
  - Implemented rate limiting on client side
  - Requested quota increases proactively
  - Designed for graceful degradation
- **Status**: Monitored continuously

**Risk 4: AI Processing Costs (MEDIUM)**
- **Impact**: Budget overruns from transcription and LLM usage
- **Probability**: MEDIUM
- **Mitigation**:
  - Implemented usage caps and rate limiting
  - Cached AI responses in DynamoDB
  - Used cost-effective AI models where possible
  - Monitored spending through AWS Cost Explorer
- **Status**: Controlled through limits

**Risk 5: Data Loss or Corruption (LOW)**
- **Impact**: Loss of meeting recordings or workspace data
- **Probability**: LOW
- **Mitigation**:
  - DynamoDB point-in-time recovery enabled
  - S3 versioning for critical files
  - Regular backup validation
  - Multi-AZ deployment for high availability
- **Status**: Protected through AWS features

#### Project Management Risks

**Risk 6: Learning Curve Challenges (MEDIUM - Occurred)**
- **Impact**: Delayed implementation due to AWS learning requirements
- **Probability**: MEDIUM (Expected for internship)
- **Mitigation Implemented**:
  - Structured AWS training modules (Week 1-7)
  - Hands-on labs before project work
  - Mentor support and code reviews
  - Community events for knowledge sharing
- **Actual Impact**: Manageable due to structured approach

**Risk 7: Scope Creep (LOW)**
- **Impact**: Project not completed within internship duration
- **Probability**: LOW
- **Mitigation**:
  - Clear feature prioritization
  - Weekly milestone tracking
  - MVP-first approach
  - Regular stakeholder communication
- **Status**: Stayed on track through disciplined planning

**Risk 8: Integration Complexity (MEDIUM - Occurred)**
- **Impact**: AWS services not integrating smoothly
- **Probability**: MEDIUM (Happened in Week 9)
- **Mitigation Implemented**:
  - Started with simple integrations first
  - Comprehensive testing of each integration
  - Detailed documentation of configurations
  - Community support through Discord/Study Group
- **Actual Impact**: Overcome through persistence and learning

#### Security Risks

**Risk 9: Authentication Vulnerabilities (MEDIUM)**
- **Impact**: Unauthorized access to workspaces and meetings
- **Probability**: MEDIUM
- **Mitigation**:
  - AWS Cognito for production-grade authentication
  - JWT token validation on all endpoints
  - Role-based access control (RBAC)
  - Regular security reviews
- **Status**: Secured through AWS best practices

**Risk 10: Data Privacy Concerns (MEDIUM)**
- **Impact**: Meeting recordings and transcripts exposed
- **Probability**: MEDIUM
- **Mitigation**:
  - Workspace-level data isolation
  - Encrypted S3 buckets (encryption at rest)
  - HTTPS everywhere (encryption in transit)
  - IAM policies following least privilege
- **Status**: Implemented privacy-first design

---

### 8. Expected Outcomes & Deliverables

#### Technical Deliverables

**✅ Completed:**

1. **Fully Functional Web Application**
   - Multi-tenant workspace platform
   - Real-time voice and text communication
   - Meeting recording and transcription
   - AI-powered meeting analysis
   - Task management and tracking
   - Workspace and team management

2. **AWS Serverless Architecture**
   - Lambda functions for all backend logic
   - DynamoDB single-table design
   - S3 storage with lifecycle policies
   - CloudFront CDN deployment
   - Cognito authentication
   - API Gateway REST endpoints

3. **Source Code Repository**
   - Well-organized Next.js frontend
   - Express.js backend on Lambda
   - Infrastructure as Code (IaC) configuration
   - Comprehensive README documentation
   - Deployment scripts and CI/CD pipeline

4. **Deployment on AWS CloudFront**
   - Production-ready deployment
   - Global CDN distribution
   - HTTPS with custom domain support
   - Optimized caching strategies
   - Monitoring and logging enabled

#### Learning Outcomes

**✅ AWS Skills Acquired:**

1. **Core AWS Services Mastery**
   - Lambda, DynamoDB, S3, CloudFront, Cognito
   - API Gateway, Step Functions, Transcribe
   - IAM, CloudWatch, Amplify
   - VPC, security groups, and networking

2. **Serverless Architecture Design**
   - Event-driven architecture patterns
   - Multi-tenant system design
   - Cost optimization strategies
   - Scalability and high availability

3. **Real-world Project Experience**
   - End-to-end application development
   - Production deployment and debugging
   - Performance optimization
   - User authentication and authorization

4. **Problem-Solving Skills**
   - CloudFront deployment troubleshooting
   - WebRTC and real-time communication
   - AWS service integration challenges
   - Production debugging techniques

**✅ Professional Development:**

1. **Community Engagement**
   - Attended 4 major AWS events
   - Participated in AWS Certification Quiz Competition
   - Learned from 20+ industry speakers
   - Built professional network in cloud community

2. **Technical Communication**
   - Comprehensive worklog documentation (12 weeks)
   - Technical proposal writing
   - Issue tracking and debugging documentation
   - Knowledge sharing with team

3. **Project Management**
   - Milestone planning and tracking
   - Time management across 12 weeks
   - Risk identification and mitigation
   - Stakeholder communication

#### Measurable Success Metrics

**Platform Capabilities:**
- ✅ Multi-workspace support (unlimited)
- ✅ Real-time voice communication (low latency)
- ✅ Text chat with message history
- ✅ Meeting recording and transcription
- ✅ AI-powered meeting analysis
- ✅ Task extraction and management
- ✅ Role-based access control
- ✅ Member invitation system

**Technical Achievements:**
- ✅ Serverless architecture (100% Lambda-based)
- ✅ Cost-optimized infrastructure ($44/month average)
- ✅ Production deployment on CloudFront
- ✅ Responsive design (mobile and desktop)
- ✅ Bilingual support (English/Vietnamese)
- ✅ Comprehensive error handling and logging

**Learning Milestones:**
- ✅ Completed 7 AWS training modules
- ✅ Hands-on experience with 10+ AWS services
- ✅ Attended 4 AWS community events
- ✅ 12 weeks of documented worklog
- ✅ Production debugging experience
- ✅ End-to-end project ownership

---

### 9. Conclusion

#### Project Summary

The **AI Meeting Workforce Platform** successfully demonstrates the power of AWS Serverless Architecture for building modern, scalable, and cost-effective applications. Over 12 weeks of intensive internship work at FCJ (April 17 - July 10, 2026), this project evolved from AWS fundamentals learning to a fully deployed production application.

**Key Achievements:**

1. **Technical Excellence**: Built a complete serverless application using 10+ AWS services with proper architecture design, cost optimization, and security best practices.

2. **Real-world Problem Solving**: Addressed the common challenge of meeting-to-execution workflow by automating transcription, analysis, and task creation.

3. **Production Deployment**: Successfully deployed to AWS CloudFront with comprehensive debugging and issue resolution, demonstrating real production engineering skills.

4. **Continuous Learning**: Progressed from AWS basics (Week 1) to complex serverless architecture, real-time communication, and AI integration (Week 12).

5. **Community Engagement**: Actively participated in 4 AWS events, learned from 20+ industry experts, and built a professional network in the cloud community.

#### Impact & Value

**For the Organization:**
- Demonstrates intern capability to deliver production-ready applications
- Showcases best practices for AWS serverless architecture
- Provides reusable patterns for multi-tenant SaaS platforms
- Creates knowledge base through comprehensive documentation

**For Personal Growth:**
- Mastered AWS core services and serverless architecture
- Gained production debugging and problem-solving experience
- Developed end-to-end project ownership skills
- Built foundation for AWS certification and career growth

**For the Community:**
- Comprehensive internship worklog serving as learning resource
- Documented real-world challenges and solutions
- Demonstrated practical application of AWS Study Group training
- Created example of structured internship approach

#### Future Enhancements

**Technical Roadmap:**
1. **Enhanced AI Capabilities**
   - Multi-language transcription support
   - Sentiment analysis for meetings
   - Automatic action item prioritization
   - Meeting outcome prediction

2. **Advanced Features**
   - Calendar integration (Google Calendar, Outlook)
   - Video recording support alongside audio
   - Advanced analytics and reporting dashboard
   - Mobile app for iOS and Android

3. **Platform Improvements**
   - SSO integration for enterprise customers
   - Advanced workspace customization
   - API for third-party integrations
   - Enhanced real-time collaboration features

4. **Infrastructure Optimization**
   - Implement AWS Step Functions for complex workflows
   - Add ElastiCache for improved performance
   - Consider EventBridge for event-driven features
   - Explore AWS AppSync for GraphQL APIs

#### Acknowledgments

I would like to express my gratitude to:

- **FCJ (First Cloud Journey)** for providing this exceptional internship opportunity and creating a supportive learning environment
- **AWS Study Group** for comprehensive training materials, hands-on labs, and community support
- **Mentors and Supervisors** for guidance, code reviews, and knowledge sharing throughout the internship
- **Event Speakers** (20+ professionals) who shared their expertise at AWS Study Group Workshop, FCAJ Community Days, and AWS Quiz Competition
- **Internship Peers** for collaboration, team project work, and mutual support during the learning journey
- **AWS Community** on Discord Lamentum for technical discussions, troubleshooting help, and encouragement

#### Final Reflection

This internship has been a transformative learning experience. Starting with AWS fundamentals and progressing to deploying a production serverless application demonstrates the power of structured learning combined with hands-on project work.

The challenges faced—especially the CloudFront deployment issues in Week 11—were invaluable learning opportunities that taught me the difference between local development and production deployment. The AWS community events provided insights from industry leaders that shaped my understanding of modern cloud architecture.

Most importantly, this project proved that with proper guidance, structured learning, and persistent effort, it's possible to build production-grade serverless applications even as an intern. The comprehensive documentation in this worklog serves not just as a record of my internship, but as a resource for future learners embarking on similar journeys.

---

### Appendices

#### A. Technology Stack Details

**Frontend Framework:**
- Next.js 16.2.9 (React 19)
- TailwindCSS 3.x
- TypeScript 5.x
- Socket.io Client 4.x
- WebRTC API

**Backend Framework:**
- Express.js 4.x
- AWS SDK v3
- TypeScript with strict mode
- Custom WebRTC signaling server

**AWS Services:**
- Compute: Lambda
- Storage: S3, DynamoDB
- AI/ML: Transcribe
- Networking: API Gateway, CloudFront
- Security: Cognito, IAM
- Monitoring: CloudWatch
- Deployment: Amplify

**Third-party APIs:**
- Google Gemini / OpenAI for LLM
- STUN/TURN servers for WebRTC

#### B. Resources & References

**AWS Study Group Resources:**
- [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)
- [Cloud Journey](https://cloudjourney.awsstudygroup.com)
- [Discord Lamentum](https://discord.gg/yjFh7dNq)
- [Facebook Community](https://www.facebook.com/groups/awsstudygroupfcj/)

**Project Resources:**
- [GitHub Worklog Repository](https://github.com/NTL0210/FACJ_Worklog)
- AWS Documentation
- Next.js Documentation
- WebRTC Documentation

**Community Events Attended:**
1. AWS Study Group Workshop (May 9, 2026)
2. FCAJ Community Day (May 23, 2026)
3. AWS Certification Quiz Competition (June 20, 2026)
4. FCAJ Community Day - AI Agents & DevOps (June 27, 2026)

#### C. Contact Information

**Intern:** Nguyễn Tấn Lộc
**Student ID:** 2280601816
**University:** HUTECH (Ho Chi Minh City University of Technology)
**Position:** FCJ Cloud Intern
**Duration:** April 17, 2026 - July 10, 2026 (12 weeks)
**GitHub:** [NTL0210](https://github.com/NTL0210)
**Worklog Website:** [https://ntl0210.github.io/NTL0210-FACJ_Worklog/](https://ntl0210.github.io/NTL0210-FACJ_Worklog/)

---

**Document Version:** 1.0
**Last Updated:** July 10, 2026
**Status:** Final Submission
