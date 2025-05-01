# 📚 AWS RDS (Relational Database Service)

## 🛠️ Core Features

- **Managed Service**:
  - Automatic provisioning
  - OS patching
  - Backups (daily + transaction logs)
  - Point-in-Time Recovery (PITR)
  - Monitoring
  - Maintenance window
  - Storage: **EBS-backed**, auto-scaling

- **Storage Auto-Resize**:
  - Triggers when:
    - <10% free space
    - >5 minutes sustained usage
    - No resize in last 6h

---

## 📤 Read Replicas

- Up to **15 asynchronous** replicas
- Use for:
  - Read scaling
  - Offloading analytics
  - Cross-AZ or cross-region replication
- **Promotion**:
  - Promote to standalone DB (update connection string)
- **Note**: Replicas are **not** used for high availability

---

## 🛡️ Multi-AZ Deployment

- **High Availability / Disaster Recovery (DR)**
  - **Synchronous replication** to standby
  - Single DNS — automatic failover
  - **Not** used for read scaling
- **Single → Multi-AZ**: One-click switch

---

## 🔄 Snapshots & Restore

- Create snapshot from any RDS instance
- Restore into new instance (optionally with new AZ, size, etc.)

---

## 🧪 RDS Custom

- For **Oracle** and **SQL Server**
- Full control of:
  - OS-level access
  - Database customization

---

## ⚡ Amazon Aurora

- **Performance**:
  - 3× faster than PostgreSQL
  - 5× faster than MySQL
- **Scalability**:
  - Storage: 10 GB – 128 TB
  - Up to 15 replicas
  - 10 ms replica lag
  - Auto healing, shared volume for write + read scaling

- **Custom Endpoints**:
  - Route traffic for:
    - Reader
    - Writer
    - Analytics

- **Global Aurora**:
  - 1 Primary + up to 5 Secondary regions
  - Up to 16 replicas per region
  - RTO (Recovery Time Objective) < 1 min

- **Aurora Cloning**:
  - Fast **copy-on-write** clone of production for staging/test

---

## 🔐 Security

- **Encryption at Rest**:
  - AWS KMS
  - Snapshots of **unencrypted** DBs can't be encrypted
  - Must restore to encrypted instance

- **Encryption in Transit**:
  - TLS

- **Authentication**:
  - **IAM** DB authentication (alternative to password)
  - **AWS Secrets Manager**

- **Networking**:
  - Control with **Security Groups**
  - No direct **SSH** access to DB

- **Audit Logs**:
  - Logins, queries (based on engine support)

---

## 🌀 RDS Proxy

- Connection pooling
- Reduce failover time by ~66%
- Use case: thousands of short-lived connections (e.g., Lambda)
- Supports:
  - MySQL
  - PostgreSQL
  - **Not** Oracle
- **Enforces IAM** and uses AWS **Secrets Manager**

---

# 🚀 AWS ElastiCache

## 🧠 Core Use Cases

- In-memory cache for stateless apps
- Read-heavy or session-heavy workloads
- Store ephemeral data

---

## 🔧 Supported Engines

### 🔹 Redis

- **Multi-AZ + Failover + AOF**
- **AOF (Append-Only File)** persistence
- Supports:
  - Sets
  - Sorted sets
- **Lazy Loading**: Load from backend on read miss
- **Write Through**: Update both cache and backend on write
- **User/Pass Auth via SASL**
- Single-threaded (but newer Redis supports multi-threaded reads)

### 🔸 Memcached

- **Multi-node** distributed cache
- No high availability (HA)
- No persistence
- Multi-threaded
- Simple key-value store

---

## 📈 Scaling & Architecture

- Horizontal scaling via **sharding**
- Read replicas
- Multi-node clusters
- Best for:
  - Session storage
  - Caching DB queries or HTML fragments
  - Real-time leaderboards or counters

---
