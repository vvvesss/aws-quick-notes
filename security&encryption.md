# AWS Security & Encryption

## Table of Contents

1. [Encryption](#encryption)  
2. [KMS Overview](#kms-overview)  
    - [KMS Key Policies](#kms-key-policies)  
    - [KMS Multi Region](#kms-multi-region)  
3. [S3 Replication and Encryption](#s3-replication-and-encryption)  
4. [AMI Sharing Process](#ami-sharing-process)
5. [SSM (System Manager) Parameter Store](#ssm-system-manager-parameter-store)
   - [Tiers](#tiers)  
6. [Secrets Manager](#aws-secrets-manager)
7. [Certificate Manager](#aws-certificate-manager)
8. [API GW Types](#api-gw-types)
9. [WebAppFirewall WAF](#aws-webappfirewall-waf)
10. [AWS Shield](#aws-shield)
11. [AWS Firewall Manager](#aws-firewall-manager)
12. [GuardDuty](#guardduty)
13. [Inspector](#inspector)
14. [Macie](#macie)

## Encryption

- Encrypt **IN-Flight** TLS/SSL
- Server Side **AT-Rest** encryption
- Client Side **AT-Rest** encryption

## KMS Overview

- Symmetric AES-256
  - single key to encrypt/decrypt
  - AWS services use such keys
  - we never get access to the key itself
- Asymmetric RSA ECC key pairs
  - Public to encrypt and Private to decrypt
  - never have access to Private : call KMS API

- AWS Owned Keys (free)
  - SSE-S3, SSE-SQS, SSE-DDB
- AWS Managed Keys (free)
  - aws/service-name, aws/rds, aws/ebs
- Customer managed keys 1$ per month

Pay for API call to KMS 0.03$ / 10000calls

- Automatic key rotation
  - Managed: auto every 1 year
  - Customer: if enable & on-demand
  - Imported: manual
  - **KMS Keys are scoped per region**

## KMS Key Policies

- Default KMS key policy, accessible by everyone in this account
- Custom
  - define user roles to access
  - define who can administer
  - used for cross account access,
  - Example: create volume from snapshot from different account: kms:CallerAccount: "target-account-id"

### KMS Multi Region

- Same ID, key, and rotations.
- Not Global. Primary and replicas.
- **Usage: encrypt in one and decrypt in other regions.**
- Not recommended. Global client side encryption on global DDB or Aurora (protect data even from db admin, example SSN).

## S3 Replication and Encryption

- SSE-S3 by default
- SSC-S3 can be replicated
- SSC-KMS enable option
  - specify which KMS key to use for encryption in target bucket
  - adapt KMS policy for target
  - IAM role with kms:decrypt for source KMS key and kms:encrypt for target KMS key
  - KMS quota and throttling
- Multi region KMS keys are treated as independent keys by AWS S3

## AMI Sharing Process

1. We have AMI in src account encrypted with src KMS key.
2. Setup the image Launch Permission to the dst AWS account.
3. Must share the KMS key to the dst account (key policy)
4. Target account IAM role/user permissions: **DescribeKey, ReEncrypt*, CreateGrant, Decrypt**

## SSM (System Manager) Parameter Store

- Config and secrets storage
- Optional KMS encryption
- Serverless, scalable, durable SDK
- Version tracking
- IAM
- EventBridge notifications
- Cloudformation integration

Store Hierarchy - folder tree like

Lambda -> IAM Role -> (GetParameters || GetParametersByPath) API  -> SSM Store

### Tiers

- Standard 4kb 10K, param policies no, free
- Advanced 8kb  100K, param policies yes, 0.05 per month per param

## AWS Secrets Manager

- Rotation of secrets every N days
- Automate generation of secrets on rotation with Lambda
- Integration with RDS
- KMS encryption

- Multi-Region secrets
- **primary - replicas**
- promote to standalone
- multi-region apps||DB||disaster

## AWS Certificate Manager

- Public/Private
- Free for public
- Auto renewal
- Integration LB, CloudFront, API GW, !!!EC2 public can't
- FQDN || *.FQDN
- DNS(auto renew -> CNAME record) || Email validation
- Imported Certificate no auto renew, but can send messages

- **AWS Config(expire check) || ACM => EventBridge => SNS || SQS || Lambda**

## API GW Types

- **Edge Optimized (default) http -> CF Edge location  -> API GW**
  - TLS must be in the same region as CF (us-east1?)
  - CNAME || Alias
- Regional - can combine with CF
  - TLS must be in same region as API Stage
  - CNAME || Alias
- Private - within VPC with ENI needs resource policy

## AWS WebAppFirewall WAF

### Can be setup on

- ALB
- API GW
- CF
- AppSync GraphQL API
- Cognito User Pool

- Web access control list
  - IP
  - HTTP headers, body, uri strings for sql inj && XSS
  - size or geo-match
  - rate based for DDoS
- Regional except CF
- Rule group
- **GA + Fixed IP -> ALB + WAF**

## AWS Shield

- Standard > free syn udp floods and attacks
- Advanced > cost much, but full features and support

## AWS Firewall Manager

- Security Policy: Set of Rules
  - WAF
  - Shield Advanced
  - SG for EC2, ALB, ENI
  - Network Firewall VPC
  - DNS Firewall
  - Region Level

Rules can be applied to new resource automatically

## GuardDuty

- VPC Flow Logs
- CloudTrail Logs
- DNS Logs
- Operational Features
  - S3 logs
  - EBS Volumes
  - Lambda Network
  - RDS Login
  - EKS Audit logs
- **Events -> EventBridge -> SNS || Lambda**

## Inspector

Automated Security Assessments

- EC2
  - System Manager Agent
  - Unintended Access
  - OS vulnerabilities
- Amazon ECR
  - Container Images Check
- Lambda
  - Software Vulnerabilities
  - Dependencies

Report to Security Hub
AWS EventBridge

## Macie

Track for PII Personally identifiable information and notify EventBridge.

- **S3 -> Macie -> EventBridge**
