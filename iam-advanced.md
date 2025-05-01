# AWS IAM Advanced

## AWS Organizations

Global service

**Multiple account management:**

- main management account
- member accounts (can be only accounts in one organization)
- billing across all accounts - single payment method
- pricing benefits for aggregated usage
- shared reserved instances and saving plans

- Root organizational unit (OU)
  - Management Account
    - OU (Dev)
      - Member Accounts
    - OU (Prod)
      - OU (HR)
      - Member Accounts

Can organize OU by:

- Business (sales, retail, finance)
- Environment (dev prod)

**Advantages:**

- Multi account vs one account and multi-vpc - better security
- tagging standards for billing purposes
- CT for all accounts
- cross account roles for admin purposes
- **Service Control Policies SCP** IAM policies applied to OU||Accounts for restriction purpose
  - **doesn't apply on management account level**
  - OU or accounts needs explicit allow
  - accounts that are declared in OU inherit its policies deny||allow (**but not explicit allow**)

## Resources based policy vs IAM roles

Assuming IAM roles give up your own original permissions.
Resources based policy can be used without giving up permissions

Example User A > scan DDB in A account and write to S3 (with resource based policy) in B account.

RBP ==> S3, SQS, SNS, Lambda, API GW

IAM Role ==> asg ec2 ecs

EventBridge -> needs resource based policy to call Lambda

EventBridge -> IAM role assumed to write to Kinesis

## IAM Permission Boundaries

Setup boundaries > even if IAM policy permission exist it have to be in the boundaries to work

### Access Logic = Organization SCP + permission boundaries + IAM policies

## IAM identity center

Single sign on to multiple accounts

Browser => AWS IAM Identity Center => Store/Retrieve Identities
=> SSO => cloud, apps, custom SAML

### Example

Iam identity center > Group Devs > assume roles with permission set of > OU Devs full access

Iam identity center > Group Devs > assume roles with permission set of > OU Prod read only

DB admins group > IAM identity center > Permission Sets (DB Admins) > IAM Role (RDS / Aurora Prod)

(ABAC) attribute based access control by users attributes like tags title locale

## AWS Directory Services

### Microsoft AD

- Windows server AD Domain services
- Object DB
  - User Accounts
  - PC
  - Printers
  - File shares
  - SG
- Centralized security, management
- Object trees
- Group of trees is a forest

Domain controller --> multiple machines

#### 3 Types

- Managed Microsoft AD: on premise <- trust -> AWS MAD <- auth
- AD Connector (proxy gw) MFA support: on premise <- proxy <- AD Connector <- auth
- Simple AD - compatible cannot be joined with on premise, stand alone

IAM AD integration - Built in for Managed AD

## AWS Control Tower

Govern a secure and compliant multi account AWS environment

Control Tower > organizations > accounts

- auto setup of environments
- auto ongoing policies management with guardrails
- detect policy violation and remediate them
- monitor compliance dashboards

### Guardrails

- preventive restrictive guardrails (SCP) example: restrict regions to all accounts
- detective guardrails AWS config example: (identify untagged resources)
