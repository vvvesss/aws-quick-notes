# AWS Solutions Architect Reference Guide

## Purpose

This document serves as a comprehensive reference for AWS service selection and architectural decision-making. While originally designed as an exam study guide, it functions as a practical engineering handbook for:

**Service Selection:** Quick decision matrices and comparison tables to choose the right AWS service for specific technical requirements, avoiding over-engineering or under-provisioning.
**Architectural Patterns:** Real-world guidance on when to combine services (e.g., CloudFront + ALB vs Global Accelerator, Kinesis Data Streams vs Firehose) based on performance, cost, and operational requirements.
**Operational Insights:** Practical tips on service limits, integration capabilities, and common gotchas that impact production deployments and system reliability.
**Cost & Performance Trade-offs:** Clear guidance on when premium services justify their cost versus when simpler alternatives suffice, helping optimize both budget and system performance.

This reference idea is to eliminates the need to dig through extensive AWS documentation when making time-sensitive architectural decisions, providing battle-tested patterns and service characteristics in a condensed, searchable format.

## Table of Contents

- [Storage Services](#storage-services)
- [Database Services](#database-services)
- [Compute Services](#compute-services)
- [Networking & Content Delivery](#networking--content-delivery)
- [Security & Identity](#security--identity)
- [Analytics & Machine Learning](#analytics--machine-learning)
- [Application Integration](#application-integration)
- [Management & Governance](#management--governance)
- [Migration & Transfer](#migration--transfer)
- [Cost Optimization](#cost-optimization)
- [General Exam Tips](#general-exam-tips)

## Storage Services

### S3 Storage Classes

**Direct upload supported:**

- S3 Standard (default)
- S3 Intelligent-Tiering
- S3 Standard-IA
- S3 One Zone-IA
- S3 Glacier Instant Retrieval
- S3 Glacier Flexible Retrieval
- S3 Glacier Deep Archive

### EBS Volume Types

| Volume Type | Max IOPS | Max Throughput | Use Case |
|-------------|----------|----------------|----------|
| gp3 | 16,000 | 1,000 MiB/s | General purpose, boot volumes |
| io2 | 64,000 | 1,000 MiB/s | High durability, IO-intensive |
| io2 Block Express | 256,000 | 4,000 MiB/s | Mission critical, ultra-high I/O |

### FSx Decision Matrix

- **S3 + high performance HPC, big data, ML** → **FSx for Lustre**
- **Multi-protocol (NFS + SMB)** → **FSx for NetApp ONTAP**
- **Windows/AD/NTFS/SMB** → **FSx for Windows File Server**
- **Linux + NFS + ZFS features** → **FSx for OpenZFS**

### Storage Gateway

| Gateway Type | Use Case | Location |
|--------------|----------|----------|
| S3 File Gateway | NFS/SMB file shares backed by S3 | On-prem |
| FSx File Gateway | SMB access to FSx for Windows | On-prem |
| Volume Gateway | iSCSI block storage backed by EBS | On-prem |
| Tape Gateway | Virtual tape library backed by S3 & Glacier | On-prem |

## Database Services

### RDS vs Aurora Comparison

| Feature | RDS Multi-AZ | Aurora Provisioned | Aurora Serverless |
|---------|--------------|-------------------|-------------------|
| Failover | <60s to standby | <30s, any reader | <30s, may have cold start |
| Read scaling | Manual read replicas | Reader endpoints | Auto ACU scaling |
| Cost | Always-on | Always-on | Pay per ACU, can pause |
| Best for | Steady workloads, simple HA | High-performance, steady | Variable/spiky workloads |

### Aurora Backup Strategy

- **Automated backups**: Short-term (max 35 days), built into Aurora
- **AWS Backup**: Long-term retention (years), compliance-friendly, cross-region

**RDS replication**:

- binary logging must be enabled for replication to work.
- automatic backups must be enabled to allow read replica creation.

### Caching Decision

**Choose Redis when:**

- Need **data structures** (sorted sets, lists, hashes)
- **High availability** with Multi-AZ
- **Persistence** and replication required
- **Pub/Sub messaging** or **ranking/leaderboards**

**Choose Memcached when:**

- **Simple key-value caching** only
- **Ultra-low latency** is critical
- **Cache can be lost** without harm
- **Easy horizontal scaling**

## Compute Services

### Deployment Options Decision

| Service | When to Use | Exam Hints |
|---------|-------------|------------|
| **Elastic Beanstalk** | Standard web apps, minimal infrastructure management | "highly available", "standard runtime", "rolling deployment" |
| **ECS/Fargate** | Containerized workloads, microservices | "Docker", "containerized", "microservices" |
| **Lambda + API Gateway** | Serverless, event-driven, short-lived | "event-driven", "serverless", "scale-to-zero" |
| **EC2** | Full control, custom/legacy apps | "custom setup", "manual tuning" |

### Auto Scaling

- **EC2 Auto Scaling**: Based on CloudWatch metrics
- **Aurora Read Replicas**: Auto scaling supported (read-only)
- **DynamoDB**: On-Demand supports automatic read/write scaling
- **Lambda Provisioned Concurrency**: Eliminates cold starts

**Auto Scaling lifecycle hooks:**

- Lifecycle running custom actions when instances launch or terminate.
- Trigger Lambda or send data

### Savings Plans

- **Compute Savings Plan**: Any EC2 family, size, region, OS
- **EC2 Instance Savings Plan**: Specific instance family in specific region

## Networking & Content Delivery

### CloudFront vs Global Accelerator

**Use CloudFront when:**

- **HTTP/S content** that can be cached
- **Video streaming** or web content
- **Static/dynamic content delivery**

**Use Global Accelerator when:**

- **Non-HTTP protocols** (VoIP, gaming, TCP/UDP)
- **Static anycast IP addresses** needed
- **Multi-region failover** without DNS TTL delays
- **Gaming/trading platforms** with unique requests

**CloudFront Functions:**

- Used to change Viewer requests and responses
- Lightweight functions written in JavaScript
- For high-scale, latency-sensitive CDN customizations

**Lambda@Edge:**

- Lambda functions written in NodeJS or Python
- Used to change CloudFront requests and responses:

### VPC Connectivity

- **VPC Peering**: Direct VPC-to-VPC connection
- **Transit Gateway**: Centralized hub for multiple VPCs
- **AWS PrivateLink**: Service-specific exposure without full routing
- **VPN**: Encrypted traffic across regions/accounts

### Load Balancer Integration

- **ALB + CloudFront**: Supports AWS WAF
- **NLB**: Does **NOT** support AWS WAF (use Shield Advanced for DDoS)
- **GLB**: Transparent Network Gateway + LB for Layer 3 traffic inspection

## Security & Identity

### Secrets Management

- **AWS KMS**: Encryption keys only, not for storing secrets
- **Parameter Store**: Configuration values, simple secrets, **no automatic rotation**
- **Secrets Manager**: Sensitive secrets with **automatic rotation**, built-in RDS integration

### IAM vs Organizations

- **IAM policies**: Grant permissions to users/roles
- **SCPs (Service Control Policies)**: Define **maximum permissions** across accounts
- **Trust relationships**: Define **who can assume** an IAM role

### Security Services

- **AWS Shield**: DDoS protection (Standard = basic, Advanced = enhanced)
- **AWS WAF**: Web application firewall for HTTP/S (ALB, CloudFront, API Gateway)
- **GuardDuty**: ML-based threat detection using CloudTrail, VPC Flow Logs, DNS logs
- **Inspector**: **Agent-required** vulnerability scanning for EC2
- **AWS Network Firewall**: DNS/domain filtering, not just IPs

## Analytics & Machine Learning

### Kinesis Services Comparison

| Service | Purpose | Latency | Storage | Management |
|---------|---------|---------|---------|------------|
| **Data Streams** | **Real-time processing** with replay | Milliseconds | Up to 365 days | Manual shards |
| **Data Firehose** | **Simple delivery** to S3/Redshift/OpenSearch | Seconds | No storage | Fully managed |
| **Data Analytics** | **Real-time SQL analytics** on streams | Real-time | No storage | Fully managed |

**Choose Data Streams for**: Custom processing, replay capability, multiple consumers
**Choose Firehose for**: Simple S3/Redshift delivery, no replay needed
**Choose Analytics for**: Real-time SQL on streaming data

### ML Services Quick Reference

- **Rekognition**: Face detection, labeling, celebrity recognition
- **Transcribe**: Audio to text
- **Polly**: Text to audio
- **Translate**: Language translations
- **Comprehend**: Natural language processing
- **SageMaker**: ML platform for developers
- **Textract**: Extract text from documents

### Data Analytics

**Data Lake (Lake Formation + Athena):**

- Serverless, query data in-place on S3
- Low operational overhead
- Ad hoc, multi-source analytics

**Redshift:**

- Managed data warehouse
- High-performance SQL on structured data
- Complex BI and dashboarding

## Application Integration

### SNS vs SQS

- **SNS**: Pub/Sub, fan-out messaging
- **SNS FIFO**: Ordering + deduplication with multiple SQS for fan-out

- **SQS**: Queue-based messaging
- **SQS backlog per instance**: Messages waiting per consumer (better than queue length alone)

### Step Functions

- Use for **complex workflows** with dependencies, retries, error handling
- Supports **manual approvals** and wait states
- **Visual workflow** for operational visibility

## Management & Governance

### AWS Config

**Typical use cases:**

- **Security & compliance**: Ensure encryption, IAM least privilege
- **Cost governance**: Check required cost allocation tags
- **Operational best practices**: Detect misconfigurations
- **Change auditing**: Historical configuration records

### Trusted Advisor Checks

- **Reserved Instance Optimization**: Long-running On-Demand → RI suggestions
- **Underutilized Instances**: Low CPU/network usage
- **Idle Load Balancers**: <100 requests in 7 days
- **Unassociated Elastic IPs**: Idle EIPs incur charges

### Resource Management

- **AWS Resource Groups Tag Editor**: Multi-region, multi-service tag queries
- **Systems Manager Session Manager**: Browser-based shell access to EC2
- **Systems Manager Run Command**: Execute commands across EC2 instances

## Migration & Transfer

### Snow Family

| Service | Capacity | Use Case |
|---------|----------|----------|
| **Snowcone** | 8 TB | Very small transfers, edge compute, pocket size |
| **Snowball Edge Storage** | 80 TB | **Medium-large datasets** |
| **Snowball Edge Compute** | 42 TB | Compute-heavy edge workloads |
| **Snowmobile** | 100 PB | Massive data center migrations |

### Database Migration

- **DMS**: Database migration with minimal downtime
- **Snowball Edge**: Can integrate with DMS for large database transfers

## Cost Optimization

### Capacity vs Cost

- **Reserved Instances**: Cost optimization for steady workloads
- **On-Demand Capacity Reservations**: Capacity assurance for events
- **Spot Instances**: Cost savings for fault-tolerant workloads

### S3 Cost Optimization

- **S3 Inventory**: Identify unencrypted objects at scale
- **S3 Batch Operations**: Bulk operations (copy with encryption)
- **Lifecycle policies**: Automatic transitions between storage classes

## General Exam Tips

### Common Patterns

1. **"High availability"** usually points to **Elastic Beanstalk** for web apps
2. **"Real-time"** often means **Kinesis Data Streams** or **Lambda**
3. **"Serverless"** suggests **Lambda**, **Fargate**, or **Aurora Serverless**
4. **"Cost optimization"** looks for **Reserved Instances**, **Spot**, or **S3 lifecycle**
5. **"Cross-region"** requires services that support it natively

### Storage tips

- If you see S3 + high performance HPC → Lustre.
- If you see multi-protocol (NFS + SMB) → ONTAP.
- If you see Windows/AD/NTFS/SMB → Windows File Server.
- If you see Linux + NFS + ZFS features → OpenZFS.

### Volume GW iSCSI backed by S3

- Cached volumes: low latency access to most recent data
- Stored volumes: entire dataset is on premise, scheduled backups to S3

### File GW (on premise local cache for frequently accessed data)

- **S3 File GW** S3 buckets are accessible NFS SMB
- **FSx File Gateway** FSx for Windows File Server access SMB / NTFS / AD

### Cost optimization

**Quick Decision Rule:**

- Running constantly? → Reserved Instance Optimization check.
- Barely used? → Idle/Underutilized check.
- Redshift involved? → Use Redshift-specific checks.
- Networking resources? → Check Idle ELBs & EIPs.

### AWS Batch Jobs

You configure compute environments ahead of time, where you specify:

- Allowed EC2 instance types (e.g., c5.4xlarge, m5.8xlarge, r5.12xlarge)
- Min, max, desired number of instances
- Spot or On-Demand preferences
- Auto choose and manages best fit instances

### Security Groups

- **Allow-only rules** (no explicit deny)
- Use **security group IDs** as sources for least privilege between tiers

### Subnet Types

- **Public subnet**: Has route to Internet Gateway (0.0.0.0/0 → IGW)
- **Private subnet**: No direct internet route, uses NAT for outbound
- **Subnets are AZ-specific** (cannot span multiple AZs)

---
14 Aug 2025 Veselin Yanev
