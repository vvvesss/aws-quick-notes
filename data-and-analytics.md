# AWS Data and Analytics

## Table of Contents

- [Athena](#athena)
- [Redshift](#redshift)
  - [Overview](#overview)
  - [Cluster](#cluster)
  - [Snapshots](#snapshots)
  - [Loading Data Sources](#loading-data-sources)
  - [Redshift Spectrum](#redshift-spectrum)
- [OpenSearch](#opensearch)
- [EMR Elastic Map Reduce](#emr-elastic-map-reduce)
  - [Node Types](#node-types)
- [Amazon QuickSight](#amazon-quicksight)
- [AWS Glue](#aws-glue)
- [AWS Data Lake](#aws-data-lake)
- [Apache Flink](#apache-flink)
  - [Backups](#backups)
- [Amazon Managed Streaming for Apache Kafka (MSK)](#amazon-managed-streaming-for-apache-kafka-msk)
  - [Kinesis DS vs MSK](#kinesis-ds-vs-msk)
- [Ingestion Pipeline](#ingestion-pipeline)

## Athena

**S3 Serverless** Presto SQL file queries CSV JSON ORX Avro Parquet

5$ per TB

Used with Quicksight for report and dashboards

FLow logs, ELB logs, Cloudtralis

**Columnar data** for costs savings via Apache Parquet  ORC (optimised row columnar)Huge performance

**Glue** to convert data to Parquet or ORC

**Compress** data smaller retrievals

**Partition** Datasets for easy queries s3://yourBucket/pathToTable/**partition_column_name**

    Example  s3://yourBucket/flight/parquet/year=1992/month=1/day=2/

Use large files > 128Mb

**Federated Query** SQL and NoSQL or custom data sources Lambda(datasource connector) to run Federated Queries (Cloudwatch logs, DDB, RDS ..)

## Redshift

### Overview

Postgresql based but not OLTransactionP it is OnLineAnalyticsProcessing OLAP

10x better performanceColumnar storage & parallel query engine

- Provisioned or Serverless
- SQL query interface
- BI Tools Amazon Quicksight Tableau

Redshift is faster queries joins than Athena (servers and S3)

### Cluster

Leader node planning -> Compute node performing

### Snapshots

MultiAz Auto(8h or every 5GB) == 35d ret > or Manual
Snapshots PITR on S3 incremental -> restore to new cluster

### Loading data sources

- Kinesis firehose (through S3 copy)> Redshift  
- S3 **copy** (internet or enhanced VPC routing) > Redshift
- EC2 JDBC driver > Redshift

### Redshift Spectrum

Redshift cluster query directly in S3 without loading it. Must have cluster available and query is submitted to 1000s of spectrum nodes

## OpenSearch

- Search any field even partially matches
- Complement to other DB
- Managed or server-less
- No native SQL support, but plugin available
- Src. Firehose, IoT, CloudWatch Logs
- Sec. Cognito IAM KMS TLS

**Patterns:**
CRUD > DDB Table > DDB Stream > Lambda > OpenSearch > EC2 > return full item from DDB

CloudWatch Logs > Subscription Filter > Lambda/KinesisDataFirehose > OpenSearch

KDS > KDF || lambda > AOS

## EMR Elastic Map Reduce

Hadoop clusters for BigData analyze and process (Hadoop Distributed File System (HDFS): Stores data across multiple machines). Provisioned made by 100s EC2

ASG Spot integration  EMR

EMR comes bundled with Apache Spark, HBase, Presto, Flink

Use cases: data processing, machine learning, web indexing, big data

### Node types

- Master Node: coordinate and manage health, long running, on-demand or reserved EC2
- Core Node: Run tasks and store data, long running, on-demand or reserved EC2
- Task Node: Just running tasks, Spot

## Amazon QuickSight

- Serverless ML BI
- Dashboards connected to Data sources
- Fast AS, embeddable, per session pricing
- Sources: RDS Aurora Athena Redshift S3 ..  or SaaS like Jira sales force … or terra data JDBC .. direct import on csv xls tsv etc elf clf
- Column Level Security CLS (column display filter)
- Users and Groups to setup access to Dashboard readonly (preserves the config of the analysis)
- Publish and share dashboards

## AWS Glue

ETL Extract Transform and Load service server less

**Usages:**

- S3 && RDS > extract > Glue > load > Redshift

Convert in Parquet format  

- insert csv file > S3 > Glue > parquet > other S3 > analyse > Athena

Data catalog

- S3 DDB RDS JDBC > Data crawler > AWS Glue Data Catalog > Athena || Redshift || EMRGlue

**Thinks to know:**

- Job Bookmarks prevent reprocessing
- Glue Elastic Views - Combine and replicate data across multiple data
- Glue DataBrew clean and normalise data
- Glue Studio: GUI
- Glue Streaming ETL: streaming jobs compatible with Kinesis DS Kafka etc

## AWS Data Lake

- Central data place
- Fully managed
- Discover Cleanse Transform Data > ingest > Data Lake
- Blueprints for S3 RDS NoSQL
- Fine grained Access Control
- Src crawlers ETL and Data prep Data catalog

**Lake Formation:**

- S3/RDS/Aurora/OnPreDB > SrcCrowl/ELT/Datadog/SecuritySettings/AccessControl > Data Lake && Atena/Redshift/EMR

**Centralized permissions**
S3/RDS/Aurora > ingest > Access Control (col level security) > data lake && athena/quicksight > users

## Apache Flink

Flink - framework for processing data streams

- Src Kinesis DS || MSK (Kafka) > Flink
- Provisioned Compute, parallel AS

### Backups

- stream transformations
cannot read from Firehose

## Amazon managed streaming for Apache Kafka MSK

Alternative to Kinesis Managed Kafka clusters

- multi Az up to 3 for HA
- Auto recovery

### Kinesis DS vs MSK

- 1Mb limit               | >= 1Mb
- Streams shards   |  Topics Partitions
- Shard splitting/merging     |   only add partition to topics
- TLS               |       Plain or TLS
- KMS at rest  |  same

Consumers may be Kinesis Data Analitics || Glue || Lambda || ECS EKS EC2

## Ingestion Pipeline

- Serverless
- Real time data collection
- transformation
- SQL query
- results in S3
- load in dashboards or warehouse

**Example:**
IoT Core > Kinesis DS > Kinesis FH (every 1 min can use lambda for transformation) > S3 > SQS > Lambda (create query) > Athena (execute query back to S3) > reporting S3 > Redshift QuickSight
