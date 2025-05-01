# AWS Database: How to Choose

## Table of Contents

- [Considerations](#considerations)
  - [RDS (Relational Database Service)](#rds-relational-database-service)
  - [Redis / Memcached (In-Memory Store)](#redis--memcached-in-memory-store)
  - [S3 (Object Store)](#s3-object-store)
  - [DocumentDB (MongoDB Compatible)](#documentdb-mongodb-compatible)
  - [Neptune (Graph Database)](#neptune-graph-database)
  - [Keyspaces (Apache Cassandra)](#keyspaces-apache-cassandra)
  - [QLDB (Quantum Ledger Database)](#qldb-quantum-ledger-database)
  - [Timestream (Time Series Database)](#timestream-time-series-database)
  - [Ingestion Pipelines](#ingestion-pipelines)
  - [Summary](#summary)

When choosing a database on AWS, consider the following factors:

## Considerations

- **Read/Write/Balance Throughput/Scale**
- **Data Size, Retention, Avg Data Size**
- **Data Durability / Source of Truth**
- **Concurrency**
- **Latency**
- **Data Model, Queries**: Joins, Structured, Semi-structured
- **License Types**: RDBMS, NoSQL, Object Store
- **Reporting**
- **Search**
- **Graphs**
- **Ledger**
- **Time Series**

---

### **RDS (Relational Database Service)**

- **Use Case**: OLTP (Online Transaction Processing)
- **Instance Options**: Provisioned instance size, EBS auto-scaling volume, Multi-AZ setup, IAM, Security Groups (SG), KMS encryption, SSL
- **Backups**:
  - Auto-backup with PITR (Point-in-Time Recovery) up to 35 days
  - Manual and scheduled backups
- **Maintenance**: Managed and scheduled maintenance
- **RDS Custom Aurora**:
  - OLTP support for MySQL, PostgreSQL
  - Separate storage from compute
  - 6 replicas, 3 AZ HA self-healing with auto-scaling
  - Multi-AZ DB clusters with read and write endpoints
  - Serverless for unpredictable/intermittent workloads
  - Global replication with <1s latency, up to 16 regions
  - DB Cloning for read append (useful with ML services like SageMaker, Comprehend)

---

### **Redis / Memcached (In-Memory Store)**

- **Use Case**: Key/Value (K/V) store, frequent reads, less writes, session data
- **Redis Features**:
  - Clustering, Multi-AZ replication, IAM integration, Redis AuthBack
  - Snapshotting with PITR, Managed & Scheduled maintenance
- **DynamoDB**:
  - Managed, serverless NoSQL DB for fast access
  - Can store session data with TTL (Time-to-Live)
  - Provisioned or on-demand capacity with autoscaling
  - Global: Active-active replication, Auto-backup with PITR, and Export/Import to S3
  - Use cases: Small documents (~100KB), distributed serverless cache, rapid schema evolution

---

### **S3 (Object Store)**

- **Use Case**: Store large objects, scalable, serverless
- **Features**:
  - Infinite scalability, with tiers: Standard, IA (Infrequent Access), Intelligent, Glacier
  - Lifecycle management, replication, encryption, versioning, MFA-delete, access logs
  - Encryption options: SSE-S3, SSE-KMS, SSE-C, TLS in transit
  - Advanced features: Multipart uploads, Transfer Acceleration, S3 Select
  - Event notifications with SNS, SQS, Lambda, EventBridge
- **Use Case**: Static files, K/V store for large files, website hosting

---

### **DocumentDB (MongoDB Compatible)**

- **Use Case**: JSON NoSQL storage
- **Features**:
  - Fully managed, HA with 3 AZ, auto-increment support
  - Managed backups and PITR
  - Compatible with MongoDB, but does not offer the same flexibility or scalability
  - Good for document-based storage and indexing

---

### **Neptune (Graph Database)**

- **Use Case**: Graph-based queries (e.g., social networks, recommendation engines, fraud detection)
- **Features**:
  - Multi-AZ, with 15 read replicas
  - Real-time ordered streams of graph data
  - Immutable system with crypto signature (block hash) for guarantees
  - Use cases: Knowledge graphs (e.g., Wikipedia), fraud detection, social networking, recommendation engines

---

### **Keyspaces (Apache Cassandra)**

- **Use Case**: NoSQL, serverless scalable database for large-scale applications
- **Features**:
  - Fully managed with multi-AZ replication
  - Autoscaling for tables, replicated 3 times across AZs
  - Supports Cassandra Query Language (CQL)
  - PITR (35 days retention), backups, and encryption
  - Use cases: IoT device data, time-series data

---

### **QLDB (Quantum Ledger Database)**

- **Use Case**: Financial transaction history tracking
- **Features**:
  - Fully managed serverless with multi-AZ replication
  - Immutable system, blockchain-like guarantees (block hash)
  - No decentralization like Amazon Managed Blockchain
  - Good for applications that require a verifiable transaction log

---

### **Timestream (Time Series Database)**

- **Use Case**: Store and analyze time-series data
- **Features**:
  - Auto-scaling, fully managed, fast serverless
  - SQL-compatible for querying recent in-memory and historical data
  - Built-in analytics functions for time-series analysis
  - Encryption in transit and at rest
  - Use cases: IoT applications, real-time analytics

---

### Ingestion Pipelines

- **Data Sources**:
  - AWS IoT, Kinesis DS, Prometheus, Telegraf, Kinesis Data Analytics, Apache Flink
- **Data Destinations**:
  - Amazon QuickSight, SageMaker, Grafana, any JDBC connection
- **Example Use Case**:
  - IoT Core > Kinesis DS > Kinesis Firehose > S3 > Lambda (data processing) > Athena (querying) > QuickSight (visualization)

---

### Summary

When choosing the right AWS database, you must weigh factors such as performance requirements, data types, durability, scalability, and backup/restore needs. AWS offers a wide range of database solutions to support OLTP, OLAP, NoSQL, graph data, time series, and more. Be sure to consider the use case and service features to find the best fit for your application.

---
