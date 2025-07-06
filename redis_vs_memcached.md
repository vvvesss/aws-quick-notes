# ElastiCache – Redis vs Memcached

## Redis

- ✅ **Multi-AZ with Auto-Failover**  
  Provides automatic failover to standby nodes in case of node or Availability Zone failure.

- ✅ **Read Replicas**  
  Supports read replicas to scale out read operations and enhance availability.

- ✅ **Data Durability (AOF Persistence)**  
  Uses Append-Only File (AOF) persistence to log write operations and ensure data durability.

- ✅ **Backup and Restore**  
  Native support for creating backups and restoring data as needed.

- ✅ **Advanced Data Structures**  
  Supports data types like **Sets** and **Sorted Sets**, useful for complex data modeling.

---

## Memcached

- ✅ **Multi-node (Sharding)**  
  Supports horizontal scaling through data partitioning (sharding) across multiple nodes.

- ⚠️ **No High Availability (No Replication)**  
  Does not provide replication or automatic failover features.

- ⚠️ **Non-Persistent**  
  Data is stored in-memory only — no persistence to disk.

- ✅ **Backup and Restore (Serverless)**  
  Supports snapshot backups in serverless modes.

- ✅ **Multi-threaded Architecture**  
  Designed for high performance using multiple threads for concurrent operations.

---

## Summary

| Feature                    | Redis                     | Memcached                    |
|----------------------------|--------------------------|------------------------------|
| High Availability          | Multi-AZ, Auto-Failover   | ❌ No replication             |
| Read Scaling               | Read Replicas             | Sharding                      |
| Persistence                | AOF, Snapshots            | ❌ Non-persistent             |
| Backup / Restore           | Supported                 | Supported (Serverless)        |
| Data Structures            | Rich (Sets, Sorted Sets)  | Simple (key-value)            |
| Architecture               | Single-threaded           | Multi-threaded                |
