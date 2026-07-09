---
title: "Running Traditional Web Applications on AWS Nitro Enclaves Without Code Modifications"
date: 2025-01-15
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Running Traditional Web Applications on AWS Nitro Enclaves Without Code Modifications

{{< figure src="/NTL0210-FACJ_Worklog/images/blog2.png" title="AWS Nitro Enclaves Architecture" >}}

Hello AWS Study Group VN!

While learning about AWS Nitro Enclaves, I came across an interesting article from AWS sharing about how to bring traditional Web applications into the Enclave environment with **almost no need to modify the source code** of the application.

Since the original document was published on AWS China, during the learning process I read, translated, and summarized the main points. If there are any inaccuracies or omissions, I hope everyone can provide feedback.

---

## WHAT IS NITRO ENCLAVES?

**Nitro Enclaves** is an isolated computing environment created from an EC2 Instance through the AWS Nitro Hypervisor.

### Main Purpose:

Process highly sensitive data such as:
- Encryption Keys
- Financial data
- Medical data
- User identity information
- Tasks requiring high security levels

### Enclave Environment Characteristics:

The special feature of Nitro Enclaves is that this environment is almost completely isolated:
- ❌ No IP address
- ❌ No Internet connection
- ❌ Cannot SSH directly
- ❌ No persistent storage

✅ All communication with the Enclave must go through **VSOCK** and the **Parent EC2 Instance**.

This is precisely what creates the high security level of Nitro Enclaves, but at the same time, it also makes it more difficult to bring existing applications into the Enclave.

---

## CHALLENGES WHEN MIGRATING WEB APPLICATIONS

Most current Web applications operate based on the **TCP/IP** protocol through HTTP or HTTPS.

Meanwhile, Nitro Enclaves does not support network communication in the usual way but only uses **VSOCK**.

### Problem:

If deployed directly, developers would have to modify quite a lot of source code to convert the entire communication mechanism from TCP/IP to VSOCK.

With systems that have been operating stably or long-standing applications, this is work that:
- ⚠️ Takes time
- ⚠️ Contains many risks
- ⚠️ Easily affects the existing logic of the application

---

## AWS PROPOSED SOLUTION

What I find interesting in the article is that AWS proposes using **SOCAT** as an intermediate proxy layer to convert between HTTP and VSOCK.

### Proxy Model:

```
┌─────────────────────────────────────────────────────────┐
│                   PARENT EC2 INSTANCE                   │
│                                                         │
│  Internet  →  HTTP Request  →  SOCAT Proxy             │
│                                    ↓                    │
│                              Convert HTTP → VSOCK       │
└─────────────────────────────┬───────────────────────────┘
                              │ VSOCK
                              ↓
┌─────────────────────────────────────────────────────────┐
│                   NITRO ENCLAVE                         │
│                                                         │
│              SOCAT Proxy  ←  VSOCK                      │
│                    ↓                                    │
│          Convert VSOCK → HTTP                           │
│                    ↓                                    │
│           Web Application (HTTP)                        │
└─────────────────────────────────────────────────────────┘
```

### How it works:

The model will consist of **two proxies**:

1. **Proxy running on Parent EC2**: 
   - Receives HTTP connections from outside
   - Converts to VSOCK to send into the Enclave

2. **Proxy running in the Enclave**: 
   - Receives VSOCK
   - Converts back to HTTP for application processing

The reverse direction is similar. If the application inside the Enclave needs to connect out, the proxy will convert traffic from HTTP to VSOCK so the Parent EC2 can access the network on behalf of the Enclave.

### Benefits:

Thanks to this approach, the application can still operate almost like running on a normal Linux server with **almost no need to modify the processing logic**.

---

## DEPLOYMENT PROCESS

According to the AWS article, the deployment process can be summarized as follows:

### Step 1: Prepare the environment
- Install Nitro Enclaves CLI and Developer Tools
- Configure Parent EC2 Instance

### Step 2: Build the application
- Build application into Docker Image
- Convert Docker Image to EIF format (Enclave Image File)

### Step 3: Launch the Enclave
- Launch Enclave using Nitro CLI
- Deploy two SOCAT proxies on Parent EC2 and Enclave

### Step 4: Test
- Access the application through the Parent Instance
- All traffic will be forwarded to the Enclave through VSOCK

---

## WHAT I FIND INTERESTING

What I appreciate about this deployment approach is that AWS doesn't require developers to rewrite the entire application just because of different communication mechanisms.

### Advantages:

✅ Can leverage existing applications  
✅ Significantly reduces cost and time  
✅ Reduces risk when moving to a more secure environment  
✅ Combines open source tools (SOCAT) with AWS services

This is also a good example of using an intermediate layer to solve compatibility problems while still ensuring the isolation and security of Nitro Enclaves.

### Notes:

Of course, this model also has some points to note:
- ⚠️ Proxy layer performance
- ⚠️ Traffic monitoring
- ⚠️ Overall architecture design to fit each system

Therefore, before applying to a real environment, it's still necessary to evaluate carefully according to the needs of each project.

---

## CONCLUSION

In my opinion, **Nitro Enclaves** is a quite interesting AWS service if you are learning about security or building systems that process sensitive data.

This article also shows a practical approach:

> **Instead of modifying the entire application to adapt to the Enclave, you can use a protocol conversion layer to significantly reduce migration effort while still maintaining the security level that Nitro Enclaves provides.**

This is the first time I've researched and compiled on this topic. If there are any omissions during translation or research, I hope everyone can provide feedback so I can improve.

---

## REFERENCES

**Original document in Chinese:**

* [AWS China Blog – Running Traditional Web Application Migration Practices in AWS Nitro Enclaves](https://aws.amazon.com/cn/blogs/china/running-traditional-web-application-migration-practices-in-aws-nitro-enclaves/)

**English documentation:**

* [AWS Nitro Enclaves Documentation](https://docs.aws.amazon.com/enclaves/)
* [AWS Nitro Enclaves User Guide](https://docs.aws.amazon.com/enclaves/latest/user/)

---

## Related Posts

- [KIRO – AWS AI Coding Assistant](../3.1-blog1/)
- [AWS Cost Estimation with AWS Pricing Calculator](../3.3-blog3/)

---

*Written during internship at FCJ (April 17 - July 10, 2026) and shared with AWS Study Group VN.*
