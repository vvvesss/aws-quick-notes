# AWS Lambda

## Execution Environment

- **Memory**: 128 MB to 10 GB
- **Max Execution Time**: 3 seconds to 15 minutes
- **Environment Variables**: Up to 4 KB
- **Temporary Storage**: `/tmp` with 512 MB by default (up to 10 GB with configuration)
- **Default Concurrency Limit**: 1,000 concurrent executions (can be increased via service quota request)

---

## Deployment

- **Maximum Deployment Package Size**:
  - **Compressed (ZIP)**: 50 MB (direct upload)
  - **Uncompressed Code + Dependencies**: 250 MB (unpacked size)

---

## Invocation Behavior

### Synchronous Invocation (e.g., API Gateway)

- If the **concurrency limit is exceeded**, the request is throttled and returns an HTTP **429 (Throttle)** error.

### Asynchronous Invocation (e.g., S3, SNS)

- If throttled, Lambda **automatically retries** the invocation with **exponential backoff**.
- Retry duration: **1 second to 5 minutes**, with a **maximum of 6 hours** of retry attempts.
- After retries are exhausted, the event is sent to a **Dead Letter Queue (DLQ)** if configured.

---

## Dead Letter Queue (DLQ)

Used for failed asynchronous invocations:

- **Stores** failed events for later inspection and reprocessing.
- **Prevents data loss** in case of function errors.
- **Helps debug** failed executions.

---

## Cold Starts

- Occur when a new execution environment is initialized.
- **Slower** due to dependency loading and initialization.

### Optimizations

- **Provisioned Concurrency**: Pre-warms a set number of instances to avoid cold starts.
- **SnapStart**: Pre-initializes the execution environment and snapshot it for faster startup.

---

## Networking

- Lambda runs **inside an AWS-managed VPC** by default.
- To access **VPC resources** (e.g., RDS, ElastiCache), you must configure:
  - **ENI (Elastic Network Interface)**
  - **IAM roles**
  - **Security Groups**

---

## Database Integration

- **RDS for PostgreSQL** and **Amazon Aurora** can trigger Lambda functions **on data changes**.
- Note: These are **data events** (via database logs), not traditional **database-level events**.  
  (Database engines themselves don’t emit events about data changes directly.)

---
