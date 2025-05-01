# AWS ElastiCache

## Table of Contents

- [Core Use Cases](#core-use-cases)
- [Supported Engines](#supported-engines)
  - [Redis](#redis)
  - [Memcached](#memcached)
- [Scaling & Architecture](#scaling--architecture)

## Core Use Cases

- In-memory cache for stateless apps
- Read-heavy or session-heavy workloads
- Store ephemeral data

---

## Supported Engines

### Redis

- **Multi-AZ + Failover + AOF**
- **AOF (Append-Only File)** persistence
- Supports:
  - Sets
  - Sorted sets
- **Lazy Loading**: Load from backend on read miss
- **Write Through**: Update both cache and backend on write
- **User/Pass Auth via SASL**
- Single-threaded (but newer Redis supports multi-threaded reads)

### Memcached

- **Multi-node** distributed cache
- No high availability (HA)
- No persistence
- Multi-threaded
- Simple key-value store

---

## Scaling & Architecture

- Horizontal scaling via **sharding**
- Read replicas
- Multi-node clusters
- Best for:
  - Session storage
  - Caching DB queries or HTML fragments
  - Real-time leaderboards or counters
