Here is the index of topics with their exact page numbers based on the presentation slides:

## Course Index & Map [PDF](https://docs.google.com/viewer?url=https://raw.githubusercontent.com/VamshiOrganization/AWS/main/Topics/in28minutes/AWSCertifiedDeveloperAssociate-Presentation.pdf#page=98)


### 1. Fundamentals & Getting Started

* **AWS & Course Overview** — Page 1
* **Active Learning & Course Approach** — Page 2
* **Cloud Basics & Challenges Before Cloud** — Page 6
* **Setting up AWS Account** — Page 12
* **Regions and Availability Zones** — Page 13
* Region Advantages & Selection Criteria — Page 18
* Availability Zone Overview — Page 20
* Region and AZ Code Examples — Page 21



---

### 2. Amazon EC2 (Elastic Compute Cloud)

* **EC2 Overview & Core Features** — Page 22
* **EC2 Instance Types** — Page 26
* **Instance Metadata & Dynamic Data Services** — Page 27
* **Security Groups** — Page 29
* Rules & Statefulness — Page 30
* Trivia & Best Practices — Page 31


* **IP Addresses (Public vs Private)** — Page 33
* Elastic IP Addresses — Page 34


* **Bootstrapping with User Data** — Page 37
* **Launch Templates** — Page 38
* **Amazon Machine Images (AMIs)** — Page 39
* Custom AMIs & AMIs Best Practices — Page 40


* **Key Pairs & Troubleshooting Connections** — Page 42
* **EC2 Scenarios & Quick Reviews** — Page 44

---

### 3. Load Balancing & Auto Scaling

* **Elastic Load Balancer (ELB) Overview** — Page 49
* **Networking Protocols (HTTP, HTTPS, TCP, TLS, UDP)** — Page 50
* **Types of Load Balancers** — Page 53
* Classic Load Balancer (CLB) — Page 54
* Application Load Balancer (ALB) — Page 55
* Network Load Balancer (NLB) — Page 75


* **Security Group Best Practices for ELB** — Page 57
* **Listeners & Listener Rules** — Page 58
* **Target Groups & Session Stickiness** — Page 60
* Connection Draining (Deregistration Delay) — Page 62
* Microservices Architecture with Target Groups — Page 63


* **Auto Scaling Groups (ASG)** — Page 67
* Auto Scaling Components & Use Cases — Page 69
* Dynamic Scaling Policies (Target Tracking, Simple, Step) — Page 71
* ASG Scenario Questions & Termination Policies — Page 73



---

### 4. Serverless Architecture

* **Serverless Fundamentals** — Page 80
* **AWS Lambda** — Page 82
* Lambda Function Limits & Pricing — Page 84
* Reserved Concurrency & Throttling — Page 85
* Execution Context & Cold Starts — Page 86
* Provisioned Concurrency — Page 87
* Synchronous vs. Asynchronous Invocation — Page 89
* Error Handling & Dead Letter Queues (DLQ) — Page 91
* Request Context Object — Page 92
* Lambda@Edge — Page 93
* Versioning & Aliases — Page 94
* Lambda Layers (SAM Integration) — Page 96
* Lambda Best Practices & Scenario Questions — Page 98


* **Amazon API Gateway** — Page 101
* REST API vs HTTP API vs WebSocket API — Page 104
* Custom Integration vs Proxy Integration — Page 106
* Endpoint Types (Edge-Optimized, Regional, Private) — Page 112
* Integration Types — Page 113
* Deployment Stages & Stage Variables — Page 116
* API Gateway Caching — Page 117
* API Gateway Authorization Approaches (IAM, Cognito, Lambda Authorizer) — Page 124
* API Gateway Scenario Questions — Page 129



---

### 5. Amazon Cognito & Identity Federation

* **Identity Federation Concepts (SAML & OpenID)** — Page 118
* **Amazon Cognito Overview** — Page 119
* Cognito User Pools — Page 120
* Cognito Identity Pools — Page 121
* User Pool Triggers — Page 122
* User Pools vs Identity Pools Comparison — Page 123



---

### 6. Storage: Amazon S3 & Glacier

* **Amazon S3 Fundamentals** — Page 132
* **Objects, Buckets & Key-Value Examples** — Page 134
* **S3 Versioning** — Page 136
* **Static Website Hosting** — Page 137
* **Bucket Policies & Resource-Based Policies** — Page 138
* **S3 Event Notifications** — Page 140
* **Access Control Lists (ACLs)** — Page 142
* **S3 Storage Classes & Cost Comparison** — Page 144
* **S3 Lifecycle Configuration** — Page 146
* **S3 Same-Region & Cross-Region Replication** — Page 147
* **S3 Consistency Model** — Page 149
* **Presigned URLs** — Page 150
* **S3 Access Points** — Page 151
* **S3 Performance Optimization (Multipart Upload, Range Fetches)** — Page 156
* **Amazon S3 Glacier & Glacier Deep Archive** — Page 159
* S3 vs S3 Glacier Comparison — Page 160
* Archive Retrieval Options — Page 161



---

### 7. Identity & Access Management (IAM)

* **IAM Core Concepts (Users, Groups, Roles, Policies)** — Page 165
* **Authorization Policies (JSON Structure)** — Page 166
* **Instance Profiles** — Page 170
* **Cross-Account Access Roles** — Page 172
* **Identity-based vs Resource-based Policies** — Page 176
* **IAM Best Practices & Scenario Questions** — Page 177

---

### 8. Data Encryption & Security (KMS & CloudHSM)

* **Data States (At Rest, In Transit, In Use)** — Page 182
* **Symmetric vs Asymmetric Encryption** — Page 184
* **AWS Key Management Service (KMS)** — Page 187
* Server-Side Encryption with KMS — Page 188
* Envelope Encryption — Page 189
* Customer Master Key (CMK) Types — Page 190
* KMS APIs & Quotas — Page 192
* KMS Integration with S3 & CloudWatch — Page 195


* **AWS CloudHSM** — Page 198
* **Server-Side Encryption Options (SSE-S3, SSE-KMS, SSE-C)** — Page 201
* **Client-Side Encryption** — Page 202

---

### 9. Networking: Amazon VPC & Hybrid Connectivity

* **Virtual Private Cloud (VPC) Fundamentals** — Page 205
* **VPC Subnets (Public vs Private)** — Page 207
* **AWS Route Tables & Internet Gateways (IGW)** — Page 209
* **NAT Gateways vs NAT Instances** — Page 212
* **Network Access Control Lists (NACLs) vs Security Groups** — Page 214
* **VPC Flow Logs** — Page 216
* **VPC Peering** — Page 217
* **AWS Managed VPN & AWS Direct Connect (DX)** — Page 218
* **VPC Endpoints (Gateway vs Interface Endpoints)** — Page 221

---

### 10. Database Fundamentals & AWS Services

* **Database Concepts (Availability, Durability, RTO, RPO)** — Page 229
* **Failover Examples & Read Replicas** — Page 235
* **Database Consistency Models** — Page 238
* **OLTP vs OLAP Architecture** — Page 241
* **Database Categories & AWS Services Overview** — Page 248
* **Amazon RDS (Relational Database Service)** — Page 251
* Multi-AZ Deployments — Page 254
* Read Replicas Comparison — Page 256
* Amazon Aurora — Page 259
* RDS Security, Scaling & Scenarios — Page 260


* **Amazon DynamoDB** — Page 268
* Tables, Items & Data Types — Page 269
* Primary Keys (Partition Key & Sort Key) — Page 271
