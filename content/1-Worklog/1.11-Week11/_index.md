---
title: "Week 11 Worklog"
date: 2026-06-29
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 Objectives:

* Continue group project development
* Deploy application to CloudFront for testing
* Troubleshoot and fix deployment-related issues
* Debug production environment problems
* Ensure application stability on cloud infrastructure

### Tasks Completed:

| Date | Task | Result | Resources | Notes |
|------|------|--------|-----------|-------|
| 29/06/2026 | - Continue project development<br>- Prepare for CloudFront deployment<br>- Initial deployment setup | - Completed feature development tasks<br>- Configured CloudFront distribution<br>- Deployed initial version to CloudFront<br>- Discovered deployment issues | [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)<br>[Cloud Journey](https://cloudjourney.awsstudygroup.com)<br>[Discord Lamentum](https://discord.gg/yjFh7dNq)<br>[GitHub Worklog](https://github.com/NTL0210/FACJ_Worklog) | CloudFront deployment start |
| 30/06/2026 | - Debug login functionality issues<br>- Investigate authentication problems<br>- Test login on CloudFront | - Identified login errors on CloudFront<br>- Debugged authentication flow<br>- Found CORS and cookie issues<br>- Worked on fixing authentication | [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)<br>[Cloud Journey](https://cloudjourney.awsstudygroup.com)<br>[Discord Lamentum](https://discord.gg/yjFh7dNq)<br>[GitHub Worklog](https://github.com/NTL0210/FACJ_Worklog) | Login errors troubleshooting |
| 01/07/2026 | - Fix voice chat functionality<br>- Debug WebRTC connections<br>- Test voice chat on CloudFront | - Discovered voice chat errors<br>- Investigated WebRTC signaling issues<br>- Fixed media stream problems<br>- Still troubleshooting connection stability | [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)<br>[Cloud Journey](https://cloudjourney.awsstudygroup.com)<br>[Discord Lamentum](https://discord.gg/yjFh7dNq)<br>[GitHub Worklog](https://github.com/NTL0210/FACJ_Worklog) | Voice chat debugging |
| 02/07/2026 | - Debug text chat channel messages<br>- Fix message delivery issues<br>- Test real-time messaging | - Identified text message delivery problems<br>- Debugged WebSocket connections<br>- Fixed message synchronization issues<br>- Improved real-time communication | [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)<br>[Cloud Journey](https://cloudjourney.awsstudygroup.com)<br>[Discord Lamentum](https://discord.gg/yjFh7dNq)<br>[GitHub Worklog](https://github.com/NTL0210/FACJ_Worklog) | Text chat errors |
| 03/07/2026 | - Fix workspace member invitation<br>- Debug invitation system<br>- Test member management features | - Discovered invite functionality errors<br>- Debugged invitation email system<br>- Fixed member permission issues<br>- Tested invitation workflow | [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)<br>[Cloud Journey](https://cloudjourney.awsstudygroup.com)<br>[Discord Lamentum](https://discord.gg/yjFh7dNq)<br>[GitHub Worklog](https://github.com/NTL0210/FACJ_Worklog) | Invitation system debugging |
| 04/07/2026 | - Continue fixing CloudFront issues<br>- Comprehensive testing<br>- Bug fixing and optimization | - Fixed multiple deployment issues<br>- Improved application stability<br>- Conducted extensive testing<br>- Documented all bugs and solutions | [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)<br>[Cloud Journey](https://cloudjourney.awsstudygroup.com)<br>[Discord Lamentum](https://discord.gg/yjFh7dNq)<br>[GitHub Worklog](https://github.com/NTL0210/FACJ_Worklog) | Bug fixing marathon |
| 05/07/2026 | - Final testing and validation<br>- Team review of fixes<br>- Weekly retrospective | - Validated all major fixes<br>- Conducted team review<br>- Documented lessons learned<br>- Planned next week's work | [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)<br>[Cloud Journey](https://cloudjourney.awsstudygroup.com)<br>[Discord Lamentum](https://discord.gg/yjFh7dNq)<br>[GitHub Worklog](https://github.com/NTL0210/FACJ_Worklog) | Week wrap-up |


### Knowledge Gained:

* **CloudFront Deployment**: Understanding challenges of deploying web applications to CloudFront CDN
* **Production Debugging**: Developed skills in debugging issues that only appear in production environments
* **Authentication in CDN**: Learned about CORS, cookies, and authentication challenges with CDN
* **WebRTC Troubleshooting**: Gained experience debugging voice chat and WebRTC connection issues
* **WebSocket with CloudFront**: Understanding WebSocket behavior through CloudFront distribution
* **Real-time Communication**: Debugging real-time features in distributed cloud environments

### Achievements:

* Successfully deployed application to CloudFront for the first time
* Identified and documented all major deployment issues
* Fixed critical login authentication problems
* Resolved voice chat WebRTC connection issues
* Fixed text chat message delivery problems
* Corrected workspace member invitation functionality
* Improved overall application stability on CloudFront
* Built comprehensive documentation of issues and solutions

### Challenges & Solutions:

* **Challenge**: Login functionality broken after CloudFront deployment
  * **Root Causes**: CORS configuration issues, cookie domain problems, authentication token handling
  * **Solutions**: Updated CORS headers, configured cookie settings for CDN, fixed token storage

* **Challenge**: Voice chat not working on CloudFront
  * **Root Causes**: WebRTC signaling server connection issues, STUN/TURN server configuration, media stream routing
  * **Solutions**: Reconfigured WebRTC signaling, updated STUN/TURN settings, fixed media stream handling

* **Challenge**: Text chat messages not delivering properly
  * **Root Causes**: WebSocket connection instability through CloudFront, message queue synchronization issues
  * **Solutions**: Improved WebSocket connection handling, implemented message retry logic, fixed synchronization

* **Challenge**: Workspace member invitation failing
  * **Root Causes**: Email service integration issues, invitation link generation problems, permission management errors
  * **Solutions**: Fixed email service configuration, corrected invitation URL generation, resolved permission assignment

### Technical Issues Encountered:

#### 1. Login Errors on CloudFront

**Symptoms:**
- Users unable to log in after CloudFront deployment
- Authentication tokens not being stored correctly
- Session management failures
- Redirect issues after login

**Debugging Process:**
- Checked browser console for errors
- Inspected network requests and responses
- Analyzed cookie and token handling
- Reviewed CloudFront cache behavior

**Root Causes:**
- CORS headers not configured correctly for CloudFront
- Cookies not being set with appropriate domain and security flags
- Authentication API endpoints being cached inappropriately
- Token storage conflicting with CDN behavior

**Solutions Implemented:**
- Updated CORS configuration for CloudFront distribution
- Set appropriate cookie flags (SameSite, Secure, Domain)
- Configured cache behavior to bypass authentication endpoints
- Fixed token storage to work with CDN architecture

#### 2. Voice Chat Functionality Errors

**Symptoms:**
- Voice chat connections failing to establish
- Audio not transmitting between users
- Connection drops and instability
- WebRTC signaling failures

**Debugging Process:**
- Examined WebRTC connection logs
- Tested STUN/TURN server connectivity
- Analyzed signaling server communication
- Inspected media stream initialization

**Root Causes:**
- WebRTC signaling server not accessible through CloudFront
- STUN/TURN server configuration incompatible with CDN
- Media stream routing issues in distributed environment
- ICE candidate gathering failures

**Solutions Implemented:**
- Reconfigured WebRTC signaling server endpoints
- Updated STUN/TURN server settings for CDN compatibility
- Implemented proper ICE candidate handling
- Added connection fallback mechanisms

#### 3. Text Chat Message Delivery Issues

**Symptoms:**
- Messages not appearing in chat channels
- Message order inconsistencies
- Delayed or missing message notifications
- Real-time synchronization problems

**Debugging Process:**
- Monitored WebSocket connection stability
- Traced message flow from sender to receiver
- Checked message queue and database logs
- Analyzed real-time synchronization logic

**Root Causes:**
- WebSocket connections unstable through CloudFront
- Message delivery not handling CDN latency properly
- Synchronization logic not accounting for distributed architecture
- Missing message retry and acknowledgment mechanisms

**Solutions Implemented:**
- Improved WebSocket connection stability and reconnection
- Implemented message acknowledgment and retry logic
- Enhanced synchronization for distributed environment
- Added client-side message queue with conflict resolution

#### 4. Workspace Invitation Errors

**Symptoms:**
- Invitation emails not being sent
- Invitation links not working correctly
- New members unable to join workspace
- Permission assignment failures

**Debugging Process:**
- Checked email service logs
- Tested invitation link generation
- Verified permission assignment logic
- Analyzed workspace access control

**Root Causes:**
- Email service integration affected by CloudFront deployment
- Invitation URLs generated with incorrect domain
- Permission assignment timing issues in distributed system
- Workspace access control not handling CDN properly

**Solutions Implemented:**
- Reconfigured email service for CDN environment
- Fixed invitation URL generation with correct domain
- Improved permission assignment timing and verification
- Updated workspace access control logic

### Lessons Learned:

1. **Local vs Production Differences**: Many issues only appear in production CloudFront environment that work fine locally

2. **CDN Complexity**: Deploying real-time applications with authentication to CDN introduces significant complexity

3. **Testing Strategy**: Need comprehensive testing strategy for CDN deployments before going to production

4. **Documentation Importance**: Documenting each issue and solution is critical for future troubleshooting

5. **Debugging Skills**: Production debugging requires different approaches than local development debugging

6. **Team Collaboration**: Complex production issues require collaborative debugging and knowledge sharing

### Resources & References:

* [AWS Study Group YouTube](https://www.youtube.com/@AWSStudyGroup)
* [Cloud Journey](https://cloudjourney.awsstudygroup.com)
* [Discord Lamentum](https://discord.gg/yjFh7dNq)
* [GitHub Worklog Repository](https://github.com/NTL0210/FACJ_Worklog)
* AWS CloudFront Documentation
* WebRTC Troubleshooting Guides
* WebSocket with CDN Best Practices

