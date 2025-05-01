# Amazon CloudWatch

## Metrics

- Every service
- monitoring var
- belongs to ns
- dimension, id, env, etc. count < 30
- timestamp
- dashboards
- custom metrics

## Streams

- Metrics > KDF > S3 > Athena
- Metrics > KDF > Redshift || OpenSearch
- 3th party Datadog, Splunk, New Relic, Sumo Logic
- Can filter metrics in the stream

## Logs

- Groups / applications
- Stream / instances | files | containers
- Expiration
- Export S3/KDS/KDF/Lambda/OpenSearch
- Encrypted by default
- KMS option
- Can filter log metrics for the keyword and create CW Alarm based on that filter

### Sources

- SDK, Cloudwatch (logs agent legacy || **unified agent** new one) not default
- Elastic Beanstalk
- ECS
- Lambda
- VPC Flow logs
- API Gateway
- CloudTrail filter based
- Route53 queries

## Logs Insights

UI for log queries

- Write queries, apply time ranges, output, export results and dashboards
- Common queries, recent, exceptions, events

### S3 Export

Batch <12 hrs API called CreateExportTask
Not in real time

### Logs Subscription

- Real time log events
- Send to KDS and KDF or Lambda
- Subscription filter

Cloud Logs > Subs Filter > KDS > KDF | KDA | EC2 | Lambda

Cloud Logs > Subs Filter > KDF > S3 | OpenSearch

Multi cloud logs > milti filters > aggregate > KDS

### Cross account Subscription

Sender acc              | Dest acc

Cloudlogs > Subs Filter | Subs Destination > KDS (dest access policy & IAM role)

## Cloudwatch Alarms

### States

- OK
- INSUFFICIENT DATA
- ALARM

### Period

- Length of time in sec to eval metric
- High resolutions 10, 20 30 sec

### Targets

- Stop, Terminate, Reboot, Recover instance
- Trigger AS action
- SNS

**Composite Alarms**
by monitoring states of multiple alarms

Logical AND OR

If the CPU is high AND Network is High ..
Reducing Alarm noise

Logs > Alarms > Alert

### Testing Alarms

````sh
aws cloudwatch set-alarm-state --alarm-name "myalarm" --state-value
ALARM --state-reason "testing purposes"
````

## AWS EventBridge

(CloudWatch events is old name)

- Cron jobs
- Event Pattern (IAM sign in event > SNS topic)

### Event Sources

- S3
- EC2
- Code Build
- Cloud Trail
- Cron

=> EventBridge > JSON event details

### Event Destinations

- Lambda
- SQS
- SNS KDS ...

- Default Event Bus
- Partner Event Bus
- Custom Event Bus

Archive and replay events.

Schema registry for known data structure.

Resource based policy

- Eventbus permissions
- Allow/Deny events from another AWS account or region
- Usecase aggragate all events in a single account

## AWS CW Insights

### Container Insights

Logs and metrics for:

- ECS
- EKS
- k8s on EC2
- Fargate ECS EKS

Granular Dashboard

Containerized version of CW agent

### Lambda Insights

Lambda layer runs next to lambda
Serverless monitoring solution

### Contributor Insights

Analyze log data - see metrics of top N contributors.
Total number of contributors and their usage

Tracking bad hosts, heaviest network consumers, most url errors

VPC flow logs > CW logs > CW contributor insights

Sample, built-in and custom rules.

### Application Insights

Automated dashboards tracking potential problems with monitored apps and related AWS services. Powered by SageMaker.
Alert generation capabilities.

## CloudTrail

### Overview

Governance, compliance and audit for AWS account.

- Enabled by default for all regions
- History of events
- History of API calls (console, sdk, cli, other aws services - IAM users/roles)
- Can be send into CT logs or S3 bucket

### Events

- Management Events - operations on resources
  - Default logging management events
  - Read/Write events
- Data events
  - Not logged by default, high volume
  - S3 object level activity
  - Lambda function execution
- CT Insights Events
  - Must be enabled and payed
  - Detect unusual activities
    - Inaccurate resource provisioning
    - Hitting service limits
    - Bursts of AWS IAM actions
    - Gaps in periodic maintenance
  - Tracking normal events to create baseline
  - Analyzing continuously write events to detect unusual patterns
  
  Events > CT Insights > Insights Events > S3 || CT Console || EventBridge

  90 Days retention on events in CT, for more send in S3 (Athena)

**Examples:**

User delete table DDB -> API call logs -> CT -> EventBridge -> alert -> SNS

User IAM AssumeRole -> IAM -> API call logs -> CT -> EventBridge -> SNS

## AWS Config

### Config Overview

Audit and record compliance of resources.
Record configuration changes over time. Can be used to trigger alerts with SNS on config changes. Per region, but can be aggregated by regions and accounts. Can be stored in S3.

### Config Rules

- Managed config rules > 75
- Custom config rules defined in AWS Lambda
  - each EBS disk type definition (gp2)
  - each instance type
- Can be eval or triggered
  - for each config change
  - on regular intervals
- Can't prevent actions from happening
- Not free 0.003 per config record, 0.001 per eval

### Config Resource

- View configuration
- View compliance
- View CT api calls

### Config rules remediation

How to detect and prevent actions that do not comply configuration

IAM Access key expired <- monitor AWS config -> trigger SSM document to revoke unused IAM -> deactivate

### Config Rules – Notifications

SG <-> monitor AWS Config -> trigger EventBridge -> Lambda, SNS, SQS, etc.

## CW vs CT vs Config

CW: Monitoring & dashboards. Event, alerts. Logs & Analysis

CT: Record API calls. Global service.

Config: Record config changes. Eval resources against rules. Timeline of changes.
