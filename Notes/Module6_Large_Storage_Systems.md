# Module VI: Large Storage Systems
**Subject:** Information Storage Management (ISM)  
**Reference:** Module 6 Notes + Distributed Systems literature (GFS, BigTable, Hadoop, S3)

---

## Introduction to Large Storage Systems

Modern applications deal with **petabyte-scale** data requiring:
- Distributed architectures.
- Fault-tolerant storage.
- High-throughput, parallel I/O.
- Cost-effective commodity hardware.

**Evolution of digital storage capacity:**

| Year | Capacity | Equivalent |
|------|---------|-----------|
| 1986 | 2.6 EB | ~1 CD-ROM/user |
| 1993 | 15.8 EB | ~4 CD-ROMs/user |
| 2000 | 54.5 EB | ~12 CD-ROMs/user |
| 2007 | 295 EB | ~61 CD-ROMs/user |

**Shift in technology:** Traditional systems (NFS, AFS) → Modern large-scale systems (GFS, Megastore, Hadoop).

---

## 6.1 Storage Models

### 6.1.1 Storage Model vs Data Model
- **Storage model:** Physical layout of data on storage media.
- **Data model:** Logical aspects in a database (how data is organized/accessed).

**Physical storage types:**
1. Local disk (fastest, least scalable).
2. Removable media (portable, moderate speed).
3. Network storage (scalable, shared).

### 6.1.2 Cell Storage Model
- Storage divided into **fixed-size cells**.
- Each object fits exactly in **one cell**.
- Reflects memory organization: primary memory = array of cells; disk = blocks.
- **Properties:**
  - **Coherence:** Data consistent across all access points.
  - **Before-or-after atomicity:** Operation either fully completes or not at all.

### 6.1.3 Journal Storage Model
- Stores **history of variables**, not just current value.
- User interacts with **journal manager**.
- **Operations:** start, read, write, commit, abort.
- **Log records** updates and serves as authoritative source for recovery.
- Foundation for modern database transaction logs and journaling file systems.

---

## 6.2 Distributed File Systems

### Why Distributed File Systems?
- **Client-server paradigm:** Share file systems across LANs.
- **Motivation:** Multi-user access, centralized management, resource sharing.
- **Focus:** Performance, compatibility, transparency.

**Early distributed file systems:** NFS, AFS, Sprite.

---

## 6.2.1 UNIX File System Characteristics

UNIX FS serves as foundation for most distributed file systems:
- **Layered design:** Flexibility and separation of concerns.
- **Hierarchical structure:** Directories for scalability.
- **Metadata via inodes:** Owner, rights, timestamps, size, data block pointers.
- **Device independence:** Abstract device access.

---

## 6.2.2 NFS (Network File System)

**NFS** = First widely used distributed file system.

- **Developed by:** Sun Microsystems (initially at UC Berkeley).
- **Design goals:**
  - Local file system semantics.
  - Easy integration with existing systems.
  - Cross-OS support (UNIX, Windows, Linux).
  - Modest performance degradation acceptable.

### NFS Architecture:
```
Client ── RPC ── Server
  │                │
Vnode Layer    NFS Server
  │                │
Local Files   Remote Files
```
- **Vnode (Virtual Node):** Layer that distinguishes local vs. remote files.
- **File Handles (fh):** Identifier for remote files (like a pointer to remote inode).
- **RPC (Remote Procedure Call):** Communication mechanism.
- **Stateless server:** Server doesn't maintain client state (NFS v2/v3).
  - **Challenge:** RPC must be idempotent (same request multiple times = same result).

### NFS Versions:
| Version | Description |
|---------|-------------|
| **NFSv2** | Stateless; basic file sharing |
| **NFSv3** | Performance improvements (async writes, large files) |
| **NFSv4** | Stateful; better security (Kerberos); ACLs; integrated locking |

---

## 6.2.3 AFS (Andrew File System)

- **Developed by:** Carnegie Mellon University (CMU) + IBM.
- **Scale:** Designed for up to **10,000 workstations**.
- **Key components:**
  - **Venus:** Client-side cache manager.
  - **Vice:** Server-side file service.

### AFS Features:
- **Persistent caching:** Workstation disks used as persistent caches.
- **Master updated only on modification** → reduced server load.
- **Secure communication:** Authentication tokens + Access Control Lists (ACLs).
- **Location transparency:** Users access files without knowing server location.
- **Scalability:** Handles tens of thousands of users.
- **Reduced admin overhead:** Users manage their own ACLs.

---

## 6.2.4 Sprite File System (SFS)

- Supports client/server caching.
- **Cache consistency:** If a file is being written by multiple clients, caching is **disabled** to prevent inconsistencies.
- **Dynamic memory sharing:** Between cache and virtual memory based on need.
- **Write-back vs write-through:** SFS uses write-back (better performance, risk of data loss on crash).

**SFS Concurrency:**
- **Sequential write sharing:** Caching allowed.
- **Concurrent write sharing:** Caching disabled; all I/O goes through server.
- **Delayed write-backs:** Improve performance but risk data loss on failure.

---

## 6.3 Parallel File Systems

### 6.3.1 GPFS (General Parallel File System)

- **Developed by:** IBM.
- **Purpose:** Parallel I/O support for clusters.
- **Scale:** Up to 4 PB storage, 4,096 disks.
- **Features:**
  - Large file + large directory support.
  - **Write-ahead logs** for reliability.
  - **Replication + RAID** for fault tolerance.

### GPFS Locking and Consistency:
- **Distributed lock manager:** Coordinates concurrent access.
- **Byte-range tokens:** Fine-grained locking for concurrent access.
- **Token revocation/upgrades:** Coordinate between multiple clients.
- **Metanodes:** Manage metadata for scalability.
- **Disk maps partitioned** for parallel allocation.

---

## 6.4 Google File System (GFS)

### Background:
- Built in late 1990s at Google.
- Stores petabytes of data using **commodity hardware**.
- Optimized for **large, append-dominant workloads**.

### 6.4.1 GFS Design Principles:
1. **Component failures are the norm** (commodity hardware fails frequently) → Need automatic recovery.
2. **Large files** (multi-GB) are common → Optimize block/chunk sizes.
3. **Sequential reads and appends** are dominant access patterns.
4. **Relaxed consistency model** → simplified implementation.

### 6.4.2 GFS Architecture:

**Components:**
```
Clients ──► Master ──► Chunk Servers (many)
                         │
                    GFS Chunks (64 MB)
```

| Component | Description |
|-----------|-------------|
| **Master** | Single master; manages metadata + chunk locations + replication |
| **Chunk Servers** | Store actual data in 64 MB chunks; report to master |
| **Clients** | Access data by contacting master for chunk locations, then directly to chunk servers |

**Key design decisions:**
- **Chunk size = 64 MB** (large chunks → fewer metadata entries; better for sequential access).
- **Replication = 3 copies** (default) on different chunk servers.
- **Single master** (simple; low metadata overhead; master failure = system halt).

### 6.4.3 GFS Write Process (5 Steps):
1. **Client contacts master** for chunk location and lease.
2. **Data sent to all replicas** (piped through chain of chunk servers).
3. **Write request sent to primary** chunk server.
4. **Primary forwards** write request to secondary servers.
5. **Acknowledgments** from all secondaries → primary acknowledges to client.

### 6.4.4 GFS Consistency:
- **Relaxed consistency model** → different clients may see different versions momentarily.
- **Defined vs undefined state:**
  - **Defined:** All clients see the same data after mutation.
  - **Undefined:** After concurrent writes, clients may see inconsistencies.
- **Checkpointing:** Regular master checkpoints for fast recovery.
- **Garbage collection:** Deleted files not immediately removed; reclaimed asynchronously.

### GFS vs Traditional File System:

| Feature | Traditional FS | GFS |
|---------|--------------|-----|
| Chunk size | 4-8 KB | 64 MB |
| Focus | Random access | Sequential/append |
| Hardware | Dedicated | Commodity |
| Master | Distributed | Single master |
| Workload | General | Analytics/logs |

---

## 6.5 Apache Hadoop

### Overview:
- **Open-source**, Java-based system for **massive data processing**.
- Components: **HDFS** (storage) + **MapReduce** (computation).
- Designed to run on commodity hardware clusters.

### 6.5.1 HDFS (Hadoop Distributed File System)

**Architecture:**
```
                ┌──────────────┐
                │   NameNode    │ ← Master
                │(metadata only)│
                └──────┬───────┘
                       │
         ┌─────────────┼─────────────┐
    ┌────┴────┐   ┌────┴────┐   ┌────┴────┐
    │DataNode │   │DataNode │   │DataNode │ ← Slaves
    └─────────┘   └─────────┘   └─────────┘
```

| Component | Description |
|-----------|-------------|
| **NameNode** | Master; stores metadata (file names, block locations); single node |
| **DataNodes** | Slaves; store actual data blocks; report heartbeats to NameNode |
| **Secondary NameNode** | Checkpoints NameNode metadata (NOT a failover) |

**HDFS Key Features:**
- **Default replication factor = 3** (three copies of every block).
- **Block size = 64 MB** (or 128 MB in newer versions).
- **Data locality optimization:** Try to run computation on node holding data.
- **Write-once, read-many:** Files are written once; optimized for reads.
- **Rack awareness:** Replicas placed on different racks for fault tolerance.

### 6.5.2 MapReduce Framework

**Concept:**
1. **Map phase:** Each mapper processes a subset of data → emits (key, value) pairs.
2. **Shuffle/Sort phase:** Framework groups all values for same key together.
3. **Reduce phase:** Each reducer processes grouped (key, [values]) → produces output.

**Example (Word Count):**
```
Input: "hello world hello"
Map: (hello,1), (world,1), (hello,1)
Shuffle: hello→[1,1], world→[1]
Reduce: hello→2, world→1
```

### 6.5.3 Hadoop Architecture Components:

| Component | Description |
|-----------|-------------|
| **Job Tracker** | Master for MapReduce; assigns tasks to task trackers |
| **Task Trackers** | Slaves; execute Map and Reduce tasks |
| **NameNode** | Master for HDFS; manages metadata |
| **DataNodes** | Slaves for HDFS; store data blocks |

**Schedulers:**
- **Fair Scheduler** (Facebook): Equal resource distribution across jobs.
- **Capacity Scheduler** (Yahoo): Queues with guaranteed capacity; multiple tenants.

---

## 6.6 BigTable (Google)

### Overview:
- **Distributed storage for large-scale structured data** at Google.
- Built on: **GFS** (for underlying storage) + **Chubby** (distributed lock service).
- **Data model:** Sparse, distributed, persistent, multidimensional sorted map.
- **Atomic row operations** (but no cross-row transactions).
- Used by: Google Earth, Google Analytics, Google Finance.

### 6.6.1 BigTable Data Model:
```
(row:string, column:string, time:int64) → string value
```

**Structure:**
- **Row:** Arbitrary string key (rows sorted lexicographically).
- **Column Family:** Group of columns with common prefix; unit of access control and storage.
- **Column Qualifier:** Individual column within a family.
- **Timestamp:** Multiple versions of cell value keyed by timestamp.

**Example:**
```
Row Key: "com.google.maps"
Column: "content:html" → "<html>..."  (timestamp: 1234567890)
Column: "anchor:cnnsi.com" → "Google Maps" (timestamp: 1234567891)
```

### 6.6.2 BigTable Components:

| Component | Description |
|-----------|-------------|
| **Master** | Manages tablet servers; assigns tablets; monitors load |
| **Tablet Servers** | Serve reads and writes for tablets they own |
| **Chubby** | Distributed lock service; master election; schema storage |

### 6.6.3 BigTable Tablet Hierarchy:
```
Root Tablet (1) → METADATA Tablets → User Tablets
```
- **Root tablet:** Contains location of all METADATA tablets; never split.
- **METADATA tablets:** Contain locations of user tablets.
- **User tablets:** Contain actual user data.
- **Clients cache tablet locations** to reduce master load.

---

## 6.7 Megastore (Google)

### Overview:
- Storage system for **online services** at Google.
- **Scale:** Handles **23 billion daily transactions**.
- **Design:** Balances consistency and availability across datacenters.
- Built on **BigTable** with **MVCC (Multi-Version Concurrency Control)**.

### 6.7.1 Megastore Design:
- **Partitioned entity groups:** Data partitioned into groups; ACID within a group.
- **Cross-group operations:** Limited consistency (best-effort, asynchronous).
- **Replication via Paxos:** Synchronous replication across datacenters.
- **Paxos consensus:** Ensures majority agreement before committing write.

### 6.7.2 Megastore Write Process (5 Steps):
1. **Get timestamp and log position** (from master).
2. **Gather operations in log entry.**
3. **Consensus via Paxos** across replicas.
4. **Update BigTable entries** with committed data.
5. **Cleanup:** Release locks; notify clients.

**Megastore vs BigTable:**
| Feature | BigTable | Megastore |
|---------|---------|----------|
| Consistency | Per-row atomicity | ACID within entity group |
| Replication | Single datacenter | Multi-datacenter (Paxos) |
| Use case | Analytics | Online transactional apps |

---

## 6.8 Cloud Storage and Amazon S3

### 6.8.1 Cloud Storage Context
- Clouds provide **vast storage and computing cycles**.
- Ideal for: Multimedia content delivery, big data analytics, backup/archival.
- **Strategies required:** Reduce access time; support real-time multimedia access.

### 6.8.2 Amazon Web Services (AWS)

**Key AWS Services:**
| Service | Description |
|---------|-------------|
| **EC2** | Elastic Compute Cloud – virtual machines |
| **S3** | Simple Storage Service – object storage |
| **SQS** | Simple Queue Service – message queuing |
| **CloudFront** | CDN (Content Delivery Network) |
| **SimpleDB** | Simple distributed database |
| **RDS** | Relational Database Service |

---

### 6.8.3 Amazon S3 (Simple Storage Service)

**Overview:**
- **Cloud-based object storage** service.
- **Objects:** 1 byte to 5 GB in size (individual files or objects).
- **Buckets:** Top-level container (like a directory); **flat namespace**.
- Shared bucket namespace across all S3 customers.
- **Not a traditional file system** (no hierarchy, no directories).

**Key features:**

| Feature | Description |
|---------|-------------|
| **API-based access** | REST/SOAP API (not rsync/WebDAV) |
| **Virtually unlimited capacity** | Scales automatically |
| **Data persistence** | 99.999999999% (11 nines) durability |
| **Scalability** | Handles millions of objects and requests |
| **Geographic distribution** | Objects replicated across multiple AZs |

**S3 Access Patterns:**
- `PUT` – Upload an object.
- `GET` – Download an object.
- `DELETE` – Remove an object.
- `LIST` – List objects in a bucket.

**S3 Limitations:**
- **Flat namespace** – no real directory hierarchy.
- **Eventual consistency** (for overwrites/deletes; new writes are immediately consistent).
- **Latency** – slower than local disk.
- Requires **application-specific integration** (not a drop-in file system).

**S3 Storage Classes:**

| Storage Class | Use Case | Availability |
|---------------|---------|-------------|
| S3 Standard | Frequently accessed data | 99.99% |
| S3 Standard-IA | Infrequent access | 99.9% |
| S3 Glacier | Long-term archival | 99.99% (restore 3-5 hours) |
| S3 Glacier Deep Archive | Years-long archival | 99.99% (restore 12+ hours) |

---

## 6.9 Comparison of Large Storage Systems

| System | Organization | Scale | Consistency | Use Case |
|--------|-------------|-------|------------|---------|
| **NFS** | Client-server | LAN | Strong (file locking) | File sharing on LAN |
| **AFS** | Client-server with caching | 10K+ clients | Eventually consistent (callbacks) | Large-scale campus file sharing |
| **GPFS** | Parallel | PB-scale clusters | Strong (distributed lock) | HPC parallel I/O |
| **GFS** | Master + chunk servers | PB+ | Relaxed | Google's analytics/logs |
| **HDFS** | NameNode + DataNodes | PB+ | Strong (per block) | Batch analytics (Hadoop) |
| **BigTable** | Master + tablet servers | EB | Per-row atomic | Structured web data |
| **Megastore** | Entity groups + Paxos | EB | ACID per entity group | Online Google services |
| **Amazon S3** | Object storage | Unlimited | Eventual | Cloud backup/archival |

---

## Key Definitions

| Term | Definition |
|------|-----------|
| **Chunk (GFS)** | 64 MB unit of GFS storage; replicated 3 times |
| **Tablet (BigTable)** | Contiguous range of rows; unit of management |
| **Entity Group (Megastore)** | Partition of data with ACID transactions |
| **Paxos** | Consensus algorithm ensuring agreement among distributed nodes |
| **MVCC** | Multi-Version Concurrency Control; multiple data versions for concurrent reads |
| **Block (HDFS)** | 64-128 MB unit of HDFS storage; replicated 3 times |
| **NameNode** | Master in HDFS; stores all metadata |
| **DataNode** | Worker in HDFS; stores actual data blocks |
| **Bucket (S3)** | Top-level container in Amazon S3 |
| **Object (S3)** | Basic unit in S3; 1B to 5GB |
| **MapReduce** | Parallel processing framework; Map → Shuffle → Reduce |
| **Cell storage** | Storage model with fixed-size cells |
| **Journal storage** | Stores history of variable states; supports recovery |

---

## Module 6 – Quick Revision Points

1. **Storage model** = physical layout; **Data model** = logical organization.
2. **Cell storage** = fixed-size cells; **Journal storage** = history of changes.
3. **NFS** = first distributed FS; client-server; RPC; stateless server.
4. **AFS** = large-scale; persistent caching; ACLs; 10,000+ users.
5. **Sprite FS** = disables caching during concurrent writes.
6. **GPFS** = IBM's parallel FS; 4 PB scale; distributed lock manager.
7. **GFS:** Single master + multiple chunk servers; chunk size = **64 MB**; replication = **3**.
8. **GFS write:** Client → Master (lease) → Data to all replicas → Write to primary → Forward to secondaries → Ack.
9. **HDFS** = open-source GFS; NameNode (master) + DataNodes (slaves); replication = **3**.
10. **Hadoop MapReduce:** Map → Shuffle/Sort → Reduce; Job Tracker + Task Trackers.
11. **BigTable:** Sparse, multi-dimensional map; row + column family + timestamp → value.
12. **BigTable hierarchy:** Root tablet → METADATA tablets → User tablets.
13. **Megastore:** ACID within entity groups; Paxos for multi-datacenter replication.
14. **Amazon S3:** Object storage; buckets + objects; flat namespace; 11-nines durability.
15. **S3** uses REST API (PUT, GET, DELETE, LIST).
16. **MapReduce scheduler:** Fair Scheduler (Facebook), Capacity Scheduler (Yahoo).

---

## Exam-Ready Comparison: GFS vs HDFS

| Feature | GFS | HDFS |
|---------|-----|------|
| Master | Single GFS Master | NameNode |
| Workers | Chunk Servers | DataNodes |
| Block size | 64 MB | 64-128 MB |
| Replication | 3 | 3 |
| Open source | No | Yes |
| Language | C++ | Java |
| Workload | Google-internal | Batch analytics |
| Fault tolerance | Automatic recovery | Automatic recovery |
