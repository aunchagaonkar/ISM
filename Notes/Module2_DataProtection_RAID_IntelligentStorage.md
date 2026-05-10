# Module II: Data Protection – RAID, Intelligent Storage System
**Subject:** Information Storage Management (ISM)  
**Reference:** Somasundaram & Shrivastava, "Information Storage and Management", Ch. 3 & 4

---

## 3.1 Introduction to RAID

**RAID** = **R**edundant **A**rray of **I**ndependent **D**isks

> Originally defined as "Redundant Array of **Inexpensive** Disks" in a 1987 paper by Patterson, Gibson, and Katz at UC Berkeley.

**Why RAID?**
- HDD (Hard Disk Drive) failures due to mechanical wear/tear and environmental factors.
- **MTBF (Mean Time Between Failure):** Measures average life expectancy of HDD in hours.
- **Problem:** Data centers deploy thousands of HDDs; probability of failure is high.

**Example:** 100 HDDs each with MTBF of 750,000 hours → Array MTBF = 750,000/100 = **7,500 hours** (one failure expected every ~312 days).

**RAID solves:**
- Data protection against HDD failures
- Improved I/O performance through parallelism

---

## 3.1 Implementation of RAID

### 3.1.1 Software RAID
- Uses host-based software to provide RAID functions.
- Implemented at OS level; no dedicated hardware controller.
- **Advantages:** Cost, simplicity.
- **Limitations:**
  1. Performance impact (extra CPU cycles for RAID calculations).
  2. Doesn't support all RAID levels.
  3. OS-specific (compatibility issues on upgrades).

### 3.1.2 Hardware RAID
- Uses specialized hardware controller on host or array.
- **Controller card RAID:** Host-based; RAID controller installed in host; uses PCI bus.
- **External RAID controller:** Array-based; interface between host and disks.

**Key functions of RAID controllers:**
1. Management and control of disk aggregations.
2. Translation of I/O requests between logical and physical disks.
3. Data regeneration on disk failures.

---

## 3.2 RAID Array Components

![Figure 3-1: Components of RAID array – Host, RAID Controller, Physical Array, Logical Array, Hard Disks](../Figures/Ch3%264/image.png)

```
Physical Array (sub-enclosures)
     ↓
Logical Array (RAID set / RAID group)
     ↓
Logical Volumes (LVs) – seen by OS as physical HDDs
```

- **Physical Array:** Sub-enclosures holding fixed number of HDDs + supporting hardware.
- **Logical Array:** Subset of disks within RAID array grouped for logical association.
- **Logical Volumes:** Presented to OS by RAID controller as if physical HDDs.

---

## 3.3 RAID Levels

RAID levels are defined based on **three core techniques:**

### 3.3.1 Striping
- **Strip:** Predefined number of contiguously addressable disk blocks on one disk.
- **Stripe:** Set of aligned strips spanning all disks in a RAID set.
- **Strip size (stripe depth):** Max data written to/read from a single HDD before next HDD is accessed.
- **Stripe width:** Number of data strips in a stripe.

```
RAID Set (3 disks):
Disk 1:  Strip A1 | Strip B1 | Strip C1
Disk 2:  Strip A2 | Strip B2 | Strip C2
Disk 3:  Strip A3 | Strip B3 | Strip C3
         ──Stripe A── ──Stripe B── ──Stripe C──
```

> Striping without parity/mirroring does **NOT** protect data.

### 3.3.2 Mirroring
- Data stored on **two different HDDs** (two copies).
- On HDD failure → data intact on surviving HDD.
- Controller uses mirror drive for data recovery.

**Characteristics:**
- Provides complete data redundancy.
- Faster recovery from disk failure.
- **Improves read performance** (both disks serve reads).
- **Write performance deteriorates** (every write = two writes).
- **Storage efficiency = 50%** (twice the storage needed).
- Preferred for **mission-critical applications**.

### 3.3.3 Parity
- Parity = mathematical construct to re-create missing data.
- Parity calculated via **bitwise XOR operation**.
- Less expensive than mirroring (1 parity disk vs. 100% duplication).

**Example with 5 disks (4 data + 1 parity):**
```
D1=3, D2=1, D3=2, D4=3 → P = 3+1+2+3 = 9 (sum/XOR)
If D3 fails: D3 = P - D1 - D2 - D4 = 9 - 3 - 1 - 3 = 2 ✓
```

**Disadvantage:** Parity recalculation on every data change → performance overhead.

---

## RAID Levels Summary

### RAID 0 – Striping (No Fault Tolerance)
- Data striped across all HDDs.
- Uses full storage capacity.
- **No data protection** (if any disk fails, all data lost).
- **Best for:** High I/O throughput applications not requiring high availability.
- **Min disks:** 2
- **Storage efficiency:** 100%

```
Disk1: A1 | B1 | C1 | D1 | E1
Disk2: A2 | B2 | C2 | D2 | E2
Disk3: A3 | B3 | C3 | D3 | E3
```

### RAID 1 – Mirroring (Full Fault Tolerance)
- Data written to two disks simultaneously (mirrored pair).
- On disk failure → RAID controller uses mirror for recovery.
- **Best for:** Applications requiring high availability (databases, OS drives).
- **Min disks:** 2
- **Storage efficiency:** 50%
- **Write penalty:** 2 (every write = 2 physical writes)

### RAID 3 – Parallel Access + Dedicated Parity Disk
- Data striped at **byte** level, parity on **dedicated disk**.
- Drives operate in parallel (all read/write simultaneously).
- Good for **large sequential data transfers** (e.g., video streaming).
- **Min disks:** 3
- **Storage efficiency:** (n-1)/n × 100%
- **Limitation:** Dedicated parity disk is a bottleneck.

### RAID 4 – Block-level Striping + Dedicated Parity Disk
- Similar to RAID 3 but striped at **block** level.
- Data disks can be accessed **independently**.
- Good **read throughput**, reasonable write throughput.
- Parity disk remains a write bottleneck.
- **Min disks:** 3

### RAID 5 – Distributed Parity (Most Common)
- Striped at block level + **distributed parity** across all disks.
- Eliminates write bottleneck of RAID 4 (no dedicated parity disk).
- **Most versatile RAID implementation.**
- **Min disks:** 3
- **Storage efficiency:** (n-1)/n × 100%

```
Disk1: A1 | B1 | C1 | D1 | Ep
Disk2: A2 | B2 | C2 | Dp | E1
Disk3: A3 | B3 | Cp | D2 | E2
Disk4: A4 | Bp | C2 | D3 | E3
Disk5: Ap | B4 | C4 | D4 | E4
```

**Best for:** Messaging, data mining, medium-performance media serving, RDBMS.

### RAID 6 – Distributed Dual Parity
- Same as RAID 5 but with **two parity elements**.
- Can survive failure of **two disks** simultaneously.
- **Min disks:** 4
- **Write penalty:** 6 (higher than RAID 5)
- **Rebuild:** Longer than RAID 5 due to dual parity sets.
- **Storage efficiency:** (n-2)/n × 100%

### Nested RAID (RAID 10 and RAID 01)

**RAID 1+0 (RAID 10) = Striped Mirror:**
- Step 1: Mirror (RAID 1)
- Step 2: Stripe across mirrored pairs (RAID 0)
- On failure: Only the mirror is rebuilt (faster recovery).
- **Best for:** OLTP, messaging, database apps requiring high I/O and availability.

**RAID 0+1 (RAID 01) = Mirrored Stripe:**
- Step 1: Stripe (RAID 0)
- Step 2: Mirror the entire stripe (RAID 1)
- On failure: **Entire stripe is faulted** → rebuild copies entire stripe.
- More vulnerable to second disk failure.

> **Key difference:** RAID 1+0 is **more resilient** than RAID 0+1 on rebuilds.

---

## 3.4 RAID Comparison Table

![Table 3-2 (Part 1): Comparison of Different RAID Types – RAID 0, 1, 3, 4](../Figures/Ch3%264/image%20copy.png)

![Table 3-2 (Part 2): Comparison of Different RAID Types – RAID 5, 6, 1+0/0+1](../Figures/Ch3%264/image%20copy%202.png)

| RAID | Min Disks | Storage Efficiency | Read Performance | Write Performance | Write Penalty | Fault Tolerance |
|------|-----------|-------------------|-----------------|-------------------|--------------|----------------|
| 0 | 2 | 100% | Very good (random+sequential) | Very good | None | None |
| 1 | 2 | 50% | Very good | Slower (must write to 2 disks) | 2 | 1 disk |
| 3 | 3 | (n-1)/n × 100% | Good (sequential) | Poor for random | High | 1 disk |
| 4 | 3 | (n-1)/n × 100% | Good | Fair | High | 1 disk |
| 5 | 3 | (n-1)/n × 100% | Very good (random) | Fair (parity overhead) | 4 | 1 disk |
| 6 | 4 | (n-2)/n × 100% | Very good | Fair | 6 | 2 disks |
| 1+0 | 4 | 50% | Very good | Good (small, random writes) | Very high | Multiple (depends) |

---

## 3.5 RAID Impact on Disk Performance

### Write Penalty
- In RAID with mirroring or parity, every write operation creates additional I/O overhead.
- **RAID 1 write penalty = 2** (every write = 2 physical writes).
- **RAID 5 write penalty = 4:**
  1. Read old parity (1 read)
  2. Read old data (1 read)
  3. Calculate new parity
  4. Write new data (1 write)
  5. Write new parity (1 write)
  Total = 2 reads + 2 writes = **4 I/Os per write**

- **RAID 6 write penalty = 6** (3 reads + 3 writes)

### 3.5.1 Application IOPS and RAID Configurations

**Formula:**
```
Disk load = (Read%) × Application IOPS + Write penalty × (Write%) × Application IOPS
```

**Example:** Application generates 5,200 IOPS (60% reads, 40% writes):

**RAID 5:**
```
Disk load = 0.6 × 5200 + 4 × (0.4 × 5200)
           = 3,120 + 8,320
           = 11,440 IOPS
```

**RAID 1:**
```
Disk load = 0.6 × 5200 + 2 × (0.4 × 5200)
           = 3,120 + 4,160
           = 7,280 IOPS
```

With HDD spec of 180 IOPS max:
- RAID 5: 11,440 / 180 = **64 disks**
- RAID 1: 7,280 / 180 = **42 disks** (even number for mirroring)

---

## 3.6 Hot Spares

- **Hot spare:** A disk in the RAID array that remains idle until a disk fails.
- When a disk fails, the RAID controller automatically starts rebuilding data onto the hot spare.
- Reduces time to complete data reconstruction (rebuild).
- Increases data availability during disk failure recovery period.

---

## Chapter 4: Intelligent Storage System

### 4.1 Components of an Intelligent Storage System

An intelligent storage system (enterprise storage array) consists of:

![Figure 4-2: Front-end command queuing – FIFO (without optimization) vs seek time optimized ordering](../Figures/Ch3%264/image%20copy%203.png)

```
Host → Front End → Cache → Back End → Physical Disks
```

### 4.1.1 Front End
- Interface between the host and the storage system.
- Consists of **front-end ports** and associated controllers.
- Manages all communication between hosts and the array.
- Supports multiple protocols: FC, iSCSI, FCIP, FCoE, SCSI.
- **Storage ports:** Points of connection between hosts and storage.
- Multiple front-end ports provide redundancy and load distribution.

### 4.1.2 Cache
- High-speed memory (DRAM) in the storage array.
- Acts as buffer between front-end and back-end operations.
- **Purpose:** Reduces disk I/O latency; improves overall performance.

**Read Cache (Read Prefetch):**

![Figure 4-4: Read hit and read miss – cache serving requests vs fetching from disk](../Figures/Ch3%264/image%20copy%204.png)

- Stores recently read data.
- Serves subsequent read requests from cache instead of disk.
- **Cache hit:** Requested data found in cache → faster access.
- **Cache miss:** Data not in cache → reads from disk.

**Write Cache:**
- Temporarily stores incoming write data.
- Acknowledges write completion to host before writing to disk.
- **Write pending:** Data in cache not yet committed to disk.
- **High watermark:** Cache usage level triggering write-back to disk.
- **Low watermark:** Cache usage level at which write-back stops.

**Cache Protection:**
- Modern arrays use **battery backup** or **capacitor-backed** power to protect cache data during power failure.
- Some arrays use **non-volatile RAM (NVRAM)** or **mirrored cache** for protection.

### 4.1.3 Back End
- Interface between cache/controllers and physical disks.
- Manages data transfer between cache and disk drives.
- Consists of back-end controllers and back-end ports.

### 4.1.4 Physical Disk
- Actual storage media (HDD, SSD, SAS, SATA, Fibre Channel drives).
- Organized in RAID groups for data protection.
- Different drive types for different performance/capacity/cost needs.

---

## 4.2 Intelligent Storage Array

### 4.2.1 High-End Storage Systems

![Figure 4-7: Active-active configuration – host with two active paths to LUN through Controllers A and B](../Figures/Ch3%264/image%20copy%205.png)

- Designed for maximum performance, availability, and scalability.
- Used by large enterprises with mission-critical workloads.
- Features:
  - Multiple redundant components (controllers, power supplies, fans).
  - High-bandwidth, low-latency cache.
  - Support for thousands of drives.
  - Online hardware upgrades without downtime.
  - Multiple protocols support.
  - Example: **EMC Symmetrix (VMAX)**.

### 4.2.2 Midrange Storage System
- Balance between cost and functionality.
- Used by medium-sized enterprises.
- Features:
  - Fewer ports and cache than high-end.
  - Supports multiple protocols.
  - RAID protection.
  - Example: **EMC CLARiiON (VNX)**.

---

## 4.3 Concepts in Practice: EMC CLARiiON and Symmetrix

### 4.3.1 CLARiiON Storage Array
- Midrange storage system.
- Block-level storage using iSCSI, FC, and FCoE.
- **Storage Processors (SP):** Dual SPs (SP A and SP B) for redundancy.
- Uses FLARE (Fibre-channel Level Advanced RAID Engine) OS.

### 4.3.2 CLARiiON CX4 Architecture
- Two Storage Processors (SPA and SPB) for Active-Passive redundancy.
- Each SP has its own cache and front-end ports.
- LUNs are "owned" by one SP; accessed through either SP.
- **Trespassing:** LUN migrates from one SP to the other on SP failure.

### 4.3.3 Managing the CLARiiON
- **Navisphere:** GUI and CLI management software.
- Manages disk allocation, RAID groups, LUNs, snapshots.

### 4.3.4 Symmetrix Storage Array
- High-end enterprise storage array.
- Designed for zero downtime and maximum performance.
- Support for both open systems and mainframe environments.

### 4.3.5 Symmetrix Component Overview
- **Multiple Directors:** Fibre Channel, SCSI, and management directors.
- **Global Memory (Cache):** Very large cache shared across all directors.
- **Back-end disk adapters** for connecting to physical disks.

### 4.3.6 Direct Matrix Architecture
- **Key innovation:** Data paths from any front-end director to any back-end disk without bottlenecks.
- Non-blocking matrix interconnect.
- Allows concurrent access to any disk from any host port.
- **Result:** Very high throughput and low latency.

```
Host 1 ─┐                    ┌─ Disk Group A
Host 2 ─┤ ──[Direct Matrix]──┤─ Disk Group B
Host 3 ─┤                    ├─ Disk Group C
Host 4 ─┘                    └─ Disk Group D
```

---

## Additional: File Systems, Volume Managers, RAID Systems

### Storage Components - Summary

| Component | Description | Location |
|-----------|-------------|----------|
| Application | Generates I/O requests | Host |
| File System | Organizes files hierarchically | Host |
| Volume Manager | Creates logical volumes from physical disks | Host |
| Device Driver | OS ↔ hardware communication | Host |
| HBA | Host to storage connectivity | Host |
| RAID Controller | Manages RAID sets, LVs | Storage array |
| Cache | High-speed DRAM buffer | Storage array |
| Physical Disks | Actual data storage | Storage array |

### Data Organization: File vs. Block vs. Object

| Type | Description | Use Case |
|------|-------------|----------|
| **Block** | Data accessed by LBA; raw disk access | Databases (Oracle, SQL Server) |
| **File** | Data accessed by file name/path; abstraction of block | File servers, NAS |
| **Object** | Data accessed by unique object identifier | Cloud storage, CAS |

---

## Storage Devices (Including Fixed Content Storage Devices)

### Fixed Content Storage / Content-Addressed Storage (CAS)
- Data that doesn't change after creation = **fixed content**.
- **Examples:** Patient X-rays, financial records, legal documents, email archives.
- **CAS:** Uses a **hash/fingerprint** (content address) to uniquely identify each object.
- Content address is calculated from the object's content → same content = same address.
- **WORM (Write Once, Read Many):** Data cannot be modified after writing.
- **Advantages:** Deduplication (identical objects stored only once), retrieval by content.

**Examples of fixed content:**
- Healthcare: Patient X-rays, medical records (archived).
- Finance: Financial records required by regulation.
- Legal: Contracts, correspondence.
- Email: Archived communications.

---

## Caches and Prefetching

### Cache in Storage Systems
- Intermediate high-speed buffer between I/O controllers and disks.
- **Types:**
  1. **Read cache:** Stores recently/frequently accessed data. Serves cache hits fast.
  2. **Write cache:** Temporarily holds incoming writes before committing to disk.

### Prefetching
- **Definition:** Anticipating future read requests and loading data into cache **before** it's requested.
- Works well for **sequential read workloads** (where next block likely follows current one).
- **Sequential prefetch algorithm:** After reading block N, automatically loads N+1, N+2, etc.
- **Disadvantage:** Less effective for random I/O; wastes cache space.

### Cache Management Policies
| Policy | Description |
|--------|-------------|
| **LRU (Least Recently Used)** | Evicts least recently used data from cache |
| **MRU (Most Recently Used)** | Keeps most recently used data in cache |
| **FIFO (First In, First Out)** | Evicts oldest cached data first |

---

## Module 2 – Quick Revision Points

1. **RAID** stands for Redundant Array of Independent Disks.
2. **Software RAID** uses OS; **Hardware RAID** uses dedicated controller.
3. **Three techniques:** Striping, Mirroring, Parity.
4. **RAID 0:** Striping only – best performance, NO fault tolerance.
5. **RAID 1:** Mirroring – 50% efficiency, best fault tolerance.
6. **RAID 5:** Distributed parity – write penalty = **4** – most versatile.
7. **RAID 6:** Dual parity – write penalty = **6** – survives 2 disk failures.
8. **RAID 10 > RAID 01** for resilience during rebuild.
9. **Write penalty formula:** RAID 1 = 2; RAID 5 = 4; RAID 6 = 6.
10. **Hot spare** = idle disk ready to replace failed disk automatically.
11. **Intelligent storage array** has: Front End → Cache → Back End → Disks.
12. **Cache protects** via NVRAM, battery backup, or mirrored cache.
13. **CAS** = Content-Addressed Storage for fixed/immutable content.
14. **Prefetching** = loading anticipated data into cache proactively.

---

## Important Formulas – Module 2

```
RAID disk load = Reads% × App_IOPS + Write_penalty × Writes% × App_IOPS

Number of disks = Disk_load / IOPS_per_disk

Storage efficiency:
  RAID 0:  100%
  RAID 1:  50%
  RAID 5:  (n-1)/n × 100%
  RAID 6:  (n-2)/n × 100%

Write Penalties:
  RAID 1 = 2
  RAID 5 = 4
  RAID 6 = 6
```

---

## Practice Numericals

### Q1: RAID Disk Load
An application generates 8,000 IOPS with 70% reads and 30% writes. Calculate disk load for:
- RAID 5: `0.7×8000 + 4×(0.3×8000) = 5600 + 9600 = 15,200 IOPS`
- RAID 1: `0.7×8000 + 2×(0.3×8000) = 5600 + 4800 = 10,400 IOPS`

### Q2: Number of Disks
Capacity: 2 TB, IOPS demand: 12,000, Disk: 200 GB, 175 IOPS max:
- C = 2000/200 = 10 disks
- I = 12000/175 = 69 disks
- **N = Max(10, 69) = 69 disks** (even number if RAID 1)

### Q3: MTBF of Array
100 disks each with MTBF = 600,000 hours:
- Array MTBF = 600,000/100 = **6,000 hours** ≈ 250 days
