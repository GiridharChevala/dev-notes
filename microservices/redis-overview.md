# Redis In-Depth Guide

## 📌 Introduction
Redis is an **open-source, in-memory key-value store** that supports persistence, replication, clustering, and advanced data structures.  
It is widely used for caching, session management, leaderboards, queues, pub/sub systems, and real-time analytics.

---

## ⚙️ Internal Implementation

### 1. Key-Value Storage
At its core, Redis stores data as **key-value pairs**:
- **Key** → always a string (unique identifier).
- **Value** → can be one of many data structures:
  - String
  - List
  - Set
  - Sorted Set (ZSet)
  - Hash
  - HyperLogLog
  - Streams

### 2. Hashes (Structured Storage)
A **Redis Hash** stores field-value pairs under a single key, useful for structured objects.

Example: Employee Record  
```redis
HSET emp:1001 name "Alice" department "HR" salary "60000"
HSET emp:1002 name "Bob" department "Engineering" salary "80000"
```

This allows efficient storage of millions of employee records as:
```
emp:1001 → { name: Alice, department: HR, salary: 60000 }
emp:1002 → { name: Bob, department: Engineering, salary: 80000 }
```

---

## 📊 How Redis Handles Millions of Records

Redis can handle **millions of records** because:
1. **In-Memory Storage** → All data is stored in RAM, ensuring nanosecond access times.
2. **Efficient Data Structures** → Hash tables internally, with dynamic resizing.
3. **Memory Optimization** → Uses compact encoding for small objects (ziplist, quicklist).
4. **Eviction Policies** → When memory is full, Redis uses:
   - **LRU (Least Recently Used)**
   - **LFU (Least Frequently Used)**
   - **TTL (Time-to-Live)**

### Cache Management Strategies
- **LRU** → Removes least recently accessed items.  
  Example: A browser cache removing old visited pages.
- **LFU** → Removes least frequently accessed items.  
  Example: News site keeping most popular articles.
- **TTL** → Removes expired data after a set time.  
  Example: Session tokens expiring after 30 minutes.

---

## 🗄 Persistence Mechanisms

Redis supports **two persistence models**:

### 1. RDB (Redis Database)
- Creates **point-in-time snapshots** of the dataset.  
- Pros: Small, compact files; fast startup.  
- Cons: Risk of data loss between snapshots.

### 2. AOF (Append Only File)
- Logs **every write operation** for replay at restart.  
- Pros: Higher durability, minimal data loss.  
- Cons: Larger files, slower recovery.

### 3. Combining RDB + AOF
- Many production systems **enable both**.  
- Redis prefers **AOF for recovery** since it’s more complete.

---

## 🔄 Replication & Clustering

### Replication
- **Master-Replica model**.  
- Replicas keep a **real-time copy** of master data.  
- Replication is in-memory, but replicas can persist using RDB/AOF.

```
   +---------+
   | Master  |
   +---------+
       |
   ------------
   |          |
+--------+  +--------+
|Replica1|  |Replica2|
+--------+  +--------+
```

### Clustering
- Redis Cluster partitions data across **multiple masters**.  
- Each master manages a subset of keys (**hash slots**: 0–16383).  
- Each master has **replicas** for fault tolerance.  

```
+---------+    +---------+    +---------+
| Master1 |    | Master2 |    | Master3 |
|Slots 0-5k|   |5k-10k   |   |10k-16k  |
+---------+    +---------+    +---------+
   |              |              |
+------+        +------+        +------+
|Replica|       |Replica|       |Replica|
+------+        +------+        +------+
```

---

## 🔍 Real-World Analogy

Think of Redis as a **school library**:
- Each **book (value)** is placed in a **locker (key)**.  
- A **hash** is like a locker with multiple compartments (fields like name, department, salary).  
- **Replication** → multiple libraries have identical sets of books.  
- **Clustering** → different libraries hold different sections (Science, Math, History).  
- **RDB** → nightly backup of the entire library.  
- **AOF** → recording every student’s borrowing activity.  
- **LRU/LFU/TTL** → removing old, less-used, or expired books.  

---

## ⚠️ Problems with Millions of Records

1. **Memory Limitations** → Data stored in RAM, may require sharding or clustering.  
2. **Eviction Issues** → Wrong eviction policy may remove important data.  
3. **Network Bottlenecks** → Frequent replication over WAN can lag.  
4. **Persistence Overhead** → Forking during RDB snapshots may slow performance.  

---

## 💡 Best Practices

- Use **clustering** for scaling writes/reads across nodes.  
- Enable **replication** for high availability.  
- Combine **RDB + AOF** for durability + fast recovery.  
- Apply the right **eviction policy (LRU/LFU/TTL)**.  
- Use **hashes for structured objects** instead of multiple string keys.  

---

## 🎤 Interview Q&A

### Q1: Why is Redis faster than a traditional database?
- Redis stores data **entirely in memory**.  
- It uses **single-threaded event loop** → no locking overhead.  
- Optimized **C data structures** → O(1) average access.

---

### Q2: How does Redis replication differ from persistence?
- **Replication** → real-time copy of master data to replicas (HA).  
- **Persistence (RDB/AOF)** → durability on disk for crash recovery.

---

### Q3: How does Redis cluster scale horizontally?
- Redis cluster uses **hash slot partitioning** (16,384 slots).  
- Each master owns a range of slots.  
- Clients compute `CRC16(key) % 16384` → determines master.  

---

### Q4: What happens if a master fails in Redis Cluster?
- Its **replica is promoted** to master automatically.  
- Cluster rebalances slots.  
- Clients redirect requests via `MOVED` response.

---

### Q5: Explain LRU vs LFU vs TTL eviction in Redis.
- **LRU** → evicts least recently used keys.  
- **LFU** → evicts least frequently used keys.  
- **TTL** → evicts expired keys automatically.  

---

### Q6: How does Redis handle millions of employee records?
- Store each employee as a **hash** under a key.  
- Efficient memory encoding for small hashes.  
- Cluster mode distributes employees across masters.  
- Replication ensures fault tolerance.

---

### FAANG-Level Questions
1. Design a caching layer with Redis for **1B user sessions**.  
2. How would you implement **rate limiting** using Redis?  
3. If Redis crashes during heavy writes, how do you **prevent data loss**?  
4. How do you choose between **RDB, AOF, or both** in production?  
5. How would you shard **100M employee records** across a Redis Cluster?  

---

## ✅ Conclusion
Redis is not just a cache but a **powerful in-memory database**.  
Its speed comes from memory-based storage, efficient data structures, replication, and clustering.  
By combining **RDB, AOF, replication, clustering, and eviction policies**, Redis can handle **millions to billions of records** reliably in real-world systems.

---
