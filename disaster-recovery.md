# Disaster Recovery && Migrations

## Disaster Recovery

### Types

- On premise -> On premise
- On Premise -> Cloud
- Cloud Region A -> Cloud Region B

### Objectives

- **RPO Recovery Point Objective:** how much we loose when disaster happens depending on how often doing backups
- **RTO Recovery Time Objective:** downtime to recover

### Strategies

- Backup and Restore
- Pilot Light
  - small version in the cloud
  - low RPO low RTO on relatively low cost
  - on premise db and server <- Route 53 -disaster-> AWS DB replica (which will cost) and standby EC2 rdy to replace on premise server on disaster event
- Warm Standby
  - full system at min size rdy for production scale on disaster
  - same like pilot light but ec2 is running on minimal scale
  - more money but better RPO and RTO
- Hot Site / Multi Site Approach
  - lowest RTO and RPO but highest costs
  - full production running on premise and in cloud
  - data replication
  - Route 53 can route requests to both locations "active active"
- Multi Cloud
  - the best disaster recovery strategy by using multi region with Aurora data replication, ELB and ASG

#### RTO Low to High

RTO Low ==> Backup & Restore ==> Pilot Light ==> Warm Standby ==> Multi Site ==> RTO High

### Tips

- Backup and Restore
  - EBS Snapshots, RDS auto backup, Snapshots
  - regular pushes to S3 and cross region replication
  - on premise to cloud with snowball and storage gateway
- HA
  - Route 53 migrate DNS from Region to Region
  - MultiAz - RDS, Elastic Cache, EFS, S3
  - Direct Connect + Site to Site on premise connection HA
- Replication
  - RDS cross region, Aurora and Global Databases
  - Data replication on premise to RDS
  - Storage Gateway
- Automation
  - CloudFormation and Elastic Beanstalk
  - Recover/Reboot EC2 on CW alarm event
  - Lambda custom automation
- Chaos
  - production testing

## Database Migration Service

- resilient
- self healing
- homogenous migration
- heterogeneous migration MS SQL -> Aurora
- CDR (Continuous Data Replication) CDC (Change Data Capture)
- Done by setting up EC2 Instance for replication task

- **Usage: Src DB -> EC2 DMS Instance -> Dst DB**

### Sources

- Oracle
- MS SQL
- Mysql
- MariaDB
- PostgreSQL
- MongoDB
- SAP
- DB2
- Azure SQL
- AWS RDS and Aurora
- AWS S3
- Document DB

### Destination

- Oracle
- MS SQL
- Mysql
- MariaDB
- PostgreSQL
- SAP
- AWS RDS
- Redshift
- DDB
- S3
- OpenSearch
- KDS
- Kafka
- DocumentDB & AWS Neptune
- Redis & Babelfish

### Schema Conversion Tool

Different engines requires SCT Schema Conversion Tool

- Example OLTP: SQL Oracle or Server to Mysql, PostgreSQL or Aurora
- **Oracle -> DMS + SCT -> Mysql**

### DMS Continuous Data Replication

**Two flows example for CDR:**

1. on premise ( Oracle DB  -> SCT server) --schema conversion--> aws vpc ( AWS RDS Mysql )
2. on premise Oracle DB -> data migration -> aws vpc ( Full Load CDC AWS DMS Replication Instance -> AWS RDS Mysql)

### DMS Multi-AZ Deployment

- DMS provision and maintain synchronously "stand by" replica in a different AZ
- Data redundancy
- Resilience
- Eliminates I/O freezes
- Minimize latency spikes

### AWS Web Console Options

- Discover and assess DMS fleet advisor will try to analyze your on premise db fleet and identify potential migration paths
- DMS Conversion Schema Tool
- Migrate
  - homogenous
  - serverless replications
  - instance-based migrations
  - endpoints of src and dst db can be setup here
  - db migration tasks

## RDS & Aurora Migrations

- RDS MySQL to Aurora migrations
  1. RDS MySQL -> Snapshot -> Aurora (replica lag)
  2. RDS Mysql -> Aurora read replica (no replica lag) -> promote Aurora to its own DB cluster $
  3. External Mysql -> Percona XtraBackup -> S3 -> create and import Aurora Mysql
  4. External Mysql -> mysql-dump -> create and import Aurora Mysql (slower)
  5. AWS DMS if both DB are up and running
- RDS PostgreSQL to Aurora migrations
  1. Snapshots
  2. Read replica
  3. External PostgreSQL -> backup S3 -> import Aurora (use aws_s3 aurora extension)
  4. AWS DMS if both DB are up and running

## On-premises Strategies

- You can download Amazon Linux AMI as VM iso format
  - And then use it in KVM, VMware, VirtualBox, Microsoft Hyper-V, Oracle VM, etc.
- VM import / export
  - migrate from / to EC2
  - DR strategy for on-premise VM's
- AWS application discovery service
  - analyze on premise for migration planning
  - utilization and dependency mapping
  - Migration Hub for tracking purpose
- AWS DMS
- AWS Server Migration Service SMS
  - incremental replication of on premise live servers to AWS (replicate the volume real time)

## AWS Backup

### Overview

- Fully managed service
- Centrally manage and automate of AWS services
- Supported svc
  - EC2 / EBS
  - S3
  - RDS / Aurora / DDB
  - DocDB / Neptune
  - EFS / FSX (Luster & Win File Server)
  - Storage GW (Volume GW)
- Cross region backups
- Cross account backups
- PITR for supported svc
- On demand and scheduled backups
- Tag-based backup policies
- Backup plans
  - frequency
  - window
  - transition to cold storage
  - retention

### Vault lock

- Enforce WORM backups cannot be deleted
- Additional layer of defense
- Prevent malicious destructive operations
- even root user cannot delete

## MGN Application Migration Service

Migration planning with AWS Application Discovery Service

- Plan migrations
- Server utilization data and dependency mapping
- Agentless discovery connector
  - vm inventory. configuration, cpu memory disk
- Application discovery agent
  - system config, performance, processes, network connections between systems
- Result AWS Migration Hub

### MGN

- re-host, lift and shift
- convert corp data center to AWS native apps
- first replicate to staging and then cut-over to production
- min downtime, reduced costs

## Transferring large datasets into AWS

### Example

Transfer 200TB data in the cloud. We have 100Mbit internet bw

- Internet / S-to-S VPN
  - immediate to setup
  - it may be slow ~ 185days
- Direct connect 1Gbps
  - to long for setup
  - 18.5days
- Snowball
  - one week for snowball to be delivered
  - can be combined with DMS
- For ongoing replication / transfers: StoS VPN or DX or DMS or DataSync

## VMware Cloud on AWS

- VMware cloud on-premise data center
- Client want to extend Data Center capacity to AWS, but keeping on-premise VMware cloud software (hybrid cloud)
- Use cases
  - migrate V-sphere based workloads to AWS
  - run production in private, public and hybrid cloud environments
  - disaster recovery strategies
  - accessing other AWS services from VMWare Cloud
