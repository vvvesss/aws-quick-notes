# AWS DynamoDB

DynamoDB is a fully managed NoSQL database service designed to handle massive workloads, including millions of requests per second, trillions of rows, and hundreds of terabytes of data.  
It supports **IAM-based access**, **auto-scaling**, and **two table classes**: **Standard** and **Infrequent Access (IA)**.

---

## Table Structure

- **Table** > Defined by a **Primary Key**.
- Supports **virtually unlimited rows**.
- **Attributes** are dynamic and can be appended at any time.
- **Maximum item size**: 400 KB.

### Attribute Types

- **Scalar**: `String`, `Number`, `Binary`, `Boolean`, `Null`
- **Document**: `List`, `Map`
- **Set**: `String Set`, `Number Set`, `Binary Set`

---

## Primary Keys

The primary key uniquely identifies each item in the table and can be:

1. **Simple Primary Key** – a single **Partition Key**
2. **Composite Primary Key** – a **Partition Key** and a **Sort Key**

### Example: `UserOrders` Table

| **Partition Key (UserID)** | **Sort Key (OrderID)** | **OrderDate** | **Amount** |
|----------------------------|------------------------|----------------|-------------|
| user1                      | order123               | 2025-04-08      | $20.00      |
| user1                      | order124               | 2025-04-09      | $30.00      |
| user2                      | order125               | 2025-04-08      | $15.00      |

### Key Terminology

| **Term**       | **Meaning**                                                                 |
|----------------|------------------------------------------------------------------------------|
| Partition Key  | Determines the physical partition where the item is stored                  |
| Sort Key       | Determines the logical order of items within a partition                    |
| Composite Key  | Combination of Partition Key + Sort Key                                     |

---

## Capacity Modes

### Provisioned Mode

- Define **Read** and **Write Capacity Units (RCUs/WCUs)**
- Supports **auto-scaling**, but may not be fast enough for rapid load spikes

### On-Demand Mode

- Pay-per-request
- Ideal for **unpredictable or spiky workloads**
- **More expensive** for consistent heavy usage

---

## Features

### DAX (DynamoDB Accelerator)

- Fully managed **in-memory cache**
- Seamless integration, microsecond response time
- **Default TTL**: 5 minutes

### Stream Processing

- Enables event-driven architectures

**Example Pipelines:**

1. `App -> DynamoDB -> DynamoDB Streams -> Lambda -> SNS | Other DynamoDB | OpenSearch`
2. `App -> DynamoDB -> Kinesis Data Streams -> Firehose -> S3 | Redshift | OpenSearch`

### Global Tables

- Multi-Region, **active-active replication**
- Requires **DynamoDB Streams** to be enabled

---

## Data Management

### TTL (Time to Live)

- Automatically deletes items after expiration
- Set by creating a column (e.g., `ExpTime`) with Unix timestamp values

### Backups

1. **Point-in-Time Recovery (PITR)**: Continuous backups for up to 35 days
2. **On-Demand**: Manual snapshots retained until deleted

---

## Integration with S3

### Export

- Requires PITR to be enabled
- Use cases: Analytics with **Athena**, ETL workflows
- Example: `DynamoDB -> S3 -> Athena`

### Import

- Supported formats: **CSV**, **JSON**, **ION**
- Path: `S3 -> DynamoDB`

---
