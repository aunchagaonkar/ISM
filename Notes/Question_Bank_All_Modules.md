# ISM Combined Question Bank – Theory + Numericals
**Subject:** Information Storage Management (ISM)  
**Modules:** I through VI | **Type:** Theory + Numerical Problems

---

## MODULE I – Introduction to Information Storage and Data Center

### Theory Questions

**Short Answer (2–4 marks):**
1. Define "Information Lifecycle." What is ILM (Information Lifecycle Management)?
2. Differentiate between structured and unstructured data with examples.
3. What is the difference between data and information?
4. List the five core elements of a data center.
5. What are the seven key requirements for data center elements?
6. What is tiered storage? Why is it used?
7. What is Zoned Bit Recording (ZBR)?
8. What is Logical Block Addressing (LBA)? How does it differ from CHS addressing?
9. Define seek time, rotational latency, and internal transfer time.
10. What is "short-stroking" in disks? What is its benefit?
11. What is a journaling file system? Give advantages and disadvantages.
12. Differentiate between block-level, file-level, and object-level data access.
13. What are the functions of a Host Bus Adapter (HBA)?
14. What is the role of a Volume Manager (LVM)?
15. List the three key management activities for storage infrastructure.

**Long Answer (6–10 marks):**
1. Explain the evolution of storage technology from DAS to IP SAN. Include key milestones.
2. Describe the components of a storage system environment. Explain the data flow from application to storage.
3. Explain the Information Lifecycle with a suitable example (e.g., sales order). Describe the three steps of ILM implementation.
4. Explain the fundamental laws governing disk performance: Little's Law and the Utilization Law. Draw the response time vs. utilization curve.
5. Describe the physical components of a hard disk drive. Explain how data is organized on a disk (tracks, sectors, cylinders).
6. What are the key challenges in managing information in modern enterprises?

---

### Numerical Problems (Module I)

**Q1.** An I/O request arrives at a disk controller at a rate of 80 IOPS. The disk service time is 6 ms.
Compute:
- a. Utilization (U)
- b. Response time (R)
- c. Average queue size (NQ)
- d. Time spent in queue

**Solution:**
```
Ra = 1/a = 1/80 = 12.5 ms
U = RS/Ra = 6/12.5 = 0.48 (48%)
R = RS/(1-U) = 6/(1-0.48) = 11.54 ms
NQ = U²/(1-U) = (0.48)²/(0.52) = 0.443
Time in queue = U × R = 0.48 × 11.54 = 5.54 ms
```

---

**Q2.** If the service time in Q1 is halved (3 ms), recompute all parameters.

**Solution:**
```
U = 3/12.5 = 0.24 (24%)
R = 3/(1-0.24) = 3.95 ms
NQ = (0.24)²/(0.76) = 0.0758
Time in queue = 0.24 × 3.95 = 0.95 ms
```

---

**Q3.** Disk specifications: Average seek time = 5 ms, 15,000 rpm, Internal transfer rate = 40 MB/s. Block size = 32 KB.
Calculate: Disk Service Time (RS) and maximum IOPS.

**Solution:**
```
E = 5 ms (seek time)
L = (0.5 × 60000) / 15000 = 2 ms (rotational latency)
X = 32 KB / 40 MB = 0.8 ms (internal transfer time)
RS = E + L + X = 5 + 2 + 0.8 = 7.8 ms
IOPS = 1/RS = 1/0.0078 = 128.2 ≈ 128 IOPS
```

---

**Q4.** A disk drive with 5 ms seek, 7,200 rpm, 40 MB/s transfer rate. Block size = 64 KB.
a. Calculate maximum IOPS.
b. Calculate response time at 96% utilization.

**Solution:**
```
E = 5 ms
L = (0.5 × 60000) / 7200 = 4.17 ms
X = 64 KB / 40 MB = 1.6 ms
RS = 5 + 4.17 + 1.6 = 10.77 ms
IOPS = 1/0.01077 = 92.85 ≈ 92 IOPS

U = 0.96
R = RS/(1-U) = 10.77/(1-0.96) = 10.77/0.04 = 269.25 ms
```

---

**Q5.** Application requires 200 GB capacity and 5,000 IOPS. Available disks: 66 GB usable capacity, max 140 IOPS. Disk utilization must stay below 60%.
Calculate minimum number of disks.

**Solution:**
```
Capacity disks: C = 200/66 = 3.03 → 4 disks
IOPS disks (at 60% util): IOPS per disk = 140 × 0.6 = 84 IOPS
I = 5000/84 = 59.52 → 60 disks
N = Max(4, 60) = 60 disks
```

---

**Q6.** Application needs 1.46 TB storage and 9,000 IOPS. Disk: 146 GB, max 180 IOPS.
Calculate minimum disks.

**Solution:**
```
C = 1.46 TB / 146 GB = 1460/146 = 10 disks
I = 9000/180 = 50 disks
N = Max(10, 50) = 50 disks
```

---

**Q7.** A SCSI controller has throughput 160 MB/s and disk service time RS = 0.3 ms. For block sizes of 4 KB and 64 KB, calculate the IOPS.

**Solution:**
```
4 KB: Transfer = 4 KB/160 MB = 0.025 ms
      IOPS = 1/(0.3 + 0.025) = 1/0.325 ms = 3,077 IOPS

64 KB: Transfer = 64 KB/160 MB = 0.4 ms
       IOPS = 1/(0.3 + 0.4) = 1/0.7 ms = 1,429 IOPS
```

---

## MODULE II – Data Protection: RAID, Intelligent Storage System

### Theory Questions

**Short Answer:**
1. Define RAID. What are the three techniques used in RAID?
2. What is the difference between Software RAID and Hardware RAID?
3. Define "Write Penalty." What is the write penalty for RAID 1, RAID 5, and RAID 6?
4. Explain the difference between RAID 1+0 and RAID 0+1. Which is more resilient?
5. What is a "Hot Spare"? How does it benefit RAID arrays?
6. Explain how parity works in RAID. What XOR operation is used?
7. What is MTBF? If 100 disks each have MTBF of 600,000 hours, what is the array MTBF?
8. What are the components of an intelligent storage system (front end, cache, back end)?
9. Explain read cache and write cache in storage arrays.
10. What is "prefetching"? When is it beneficial?
11. What is Content-Addressed Storage (CAS)? Give two examples of fixed content.
12. Explain the difference between RAID 3 and RAID 4.
13. What is the RAID 5 write penalty calculation (step-by-step)?
14. What are the functions of an external RAID controller?
15. Explain "Active-Active" vs "Active-Passive" storage array configurations.

**Long Answer:**
1. Describe RAID 0, 1, 5, 6, and 10. Include: minimum disks, storage efficiency, read/write performance, write penalty, and fault tolerance for each.
2. How does RAID impact disk performance? Explain write penalty with a RAID 5 example. Use XOR operations.
3. Explain the components of an intelligent storage system with a diagram. Describe the role of cache, front end, and back end.
4. A storage system uses RAID 5. Explain how data is recovered after a single disk failure using parity.

---

### Numerical Problems (Module II)

**Q1.** An application generates 5,200 IOPS with 60% reads. Calculate disk load for:
a. RAID 5 (write penalty = 4)
b. RAID 1 (write penalty = 2)

**Solution:**
```
a. RAID 5: 0.6 × 5200 + 4 × (0.4 × 5200) = 3120 + 8320 = 11,440 IOPS
b. RAID 1: 0.6 × 5200 + 2 × (0.4 × 5200) = 3120 + 4160 = 7,280 IOPS
```

---

**Q2.** Using Q1 values, if HDD spec = 180 IOPS max, how many disks needed?

**Solution:**
```
RAID 5: 11440 / 180 = 63.6 → 64 disks
RAID 1: 7280 / 180 = 40.44 → 42 disks (even, for mirroring)
```

---

**Q3.** An application generates 8,000 IOPS with 70% reads and 30% writes. Compute disk load for:
a. RAID 5   b. RAID 6   c. RAID 1

**Solution:**
```
a. RAID 5: 0.7×8000 + 4×(0.3×8000) = 5600 + 9600 = 15,200 IOPS
b. RAID 6: 0.7×8000 + 6×(0.3×8000) = 5600 + 14400 = 20,000 IOPS
c. RAID 1: 0.7×8000 + 2×(0.3×8000) = 5600 + 4800 = 10,400 IOPS
```

---

**Q4.** Storage requirement: 2 TB capacity, 12,000 IOPS demand. Disk: 200 GB, 175 IOPS max.
Calculate minimum number of disks.

**Solution:**
```
C = 2000 GB / 200 GB = 10 disks
I = 12000 / 175 = 68.57 → 69 disks
N = Max(10, 69) = 69 disks
(For RAID 1, round to nearest even: 70 disks)
```

---

**Q5.** An array has 100 disks each with MTBF = 750,000 hours.
a. Calculate array MTBF.
b. How many days until expected failure?

**Solution:**
```
a. Array MTBF = 750000/100 = 7500 hours
b. 7500 / 24 = 312.5 days
```

---

**Q6.** RAID 5 with 5 disks (4 data + 1 parity). Parity formula: P = D1 ⊕ D2 ⊕ D3 ⊕ D4.
Data: D1=1011, D2=1100, D3=0110, D4=1001. Calculate parity P.
If D3 fails, recover D3 using P.

**Solution:**
```
P = 1011 ⊕ 1100 = 0111; 0111 ⊕ 0110 = 0001; 0001 ⊕ 1001 = 1000
P = 1000

Recovery of D3 (when D3 fails):
D3 = P ⊕ D1 ⊕ D2 ⊕ D4 = 1000 ⊕ 1011 ⊕ 1100 ⊕ 1001 = 0110 ✓
```

---

## MODULE III – DAS, SCSI, SAN, NAS

### Theory Questions

**Short Answer:**
1. What is DAS? Differentiate between Internal and External DAS.
2. Compare IDE/ATA with SCSI (at least 4 differences).
3. What is SATA? How does it differ from PATA?
4. Explain the SCSI Initiator-Target (client-server) model.
5. Define SCSI LUN (Logical Unit Number).
6. What are the three FC topologies? Compare FC-AL with FC-SW.
7. What is an FC Director? How does it differ from an FC Switch?
8. Explain World Wide Names (WWN) in Fibre Channel.
9. What are FC Ports? List and describe N_port, F_port, E_port, FL_port.
10. What is zoning in SAN? Compare port-based and WWN-based zoning.
11. Define ISL (Inter-Switch Link). What is it used for?
12. What is NAS? How does it differ from SAN?
13. Compare NFS and CIFS (at least 5 differences).
14. Explain FC login types: FLOGI, PLOGI, PRLI.
15. What is iSCSI? How does it work? What is an IQN?
16. What is FCIP? How does it differ from iSCSI?
17. Explain the FC protocol stack (FC-0 through FC-4).
18. Describe the FC Frame structure. What does the CRC field do?
19. What is BB_Credit in Fibre Channel? What is its purpose?
20. Explain Core-Edge SAN topology.

**Long Answer:**
1. Explain SAN architecture in detail. Include components, topologies, FC protocol, zoning, and benefits.
2. Compare DAS, SAN, and NAS. Include connectivity, access type, protocols, use cases, advantages, and disadvantages.
3. Describe the iSCSI architecture. How do initiators and targets communicate? Include PDU structure, naming, and session establishment.
4. Explain SCSI command model. Describe CDB structure, initiator-target interaction, and SCSI addressing.
5. Explain FC Classes of Service. When is Class 3 used and why?

---

## MODULE IV – Network Components

### Theory Questions

**Short Answer:**
1. What is the difference between a switch and a director in SAN networking?
2. What is "five nines" availability? Calculate annual downtime.
3. What is FSPF? What is its role in FC networks?
4. Define link aggregation. What standard governs it?
5. What is Metro Ethernet? Name three services it provides.
6. What is InfiniBand? How does it differ from Ethernet?
7. What is RDMA? What are its advantages?
8. Compare 1GE and 10GE Ethernet for storage applications.
9. What is FCoE? What hardware does it require?
10. What is SNMP? How is it used in SAN management?
11. Explain NPIV (N_Port ID Virtualization).
12. List factors affecting high availability in a data center.

**Long Answer:**
1. Explain high availability in storage networks. How do switches, directors, redundant paths, and multipathing contribute to availability?
2. Compare Fibre Channel, 10GE Ethernet, and InfiniBand for storage interconnect. Include speed, latency, use case, and cost.
3. Describe Metro Ethernet. What are its benefits over traditional WAN technologies? Explain E-Line, E-LAN, and E-Tree services.

---

### Availability Numericals (Module IV)

**Q1.** A data center has 4.5 hours of downtime per year.
Calculate availability percentage. What level of "nines" is achieved?

**Solution:**
```
Total hours/year = 365 × 24 = 8760 hours
Availability = (8760 - 4.5)/8760 × 100 = 8755.5/8760 × 100 = 99.9486%
≈ 3 nines (99.9%)
```

---

**Q2.** A network router has MTBF = 50,000 hours and MTTR = 8 hours.
Calculate availability.

**Solution:**
```
Availability = MTBF / (MTBF + MTTR) = 50000 / (50000 + 8) = 50000/50008 = 99.984%
```

---

**Q3.** Two ISLs connect two FC switches. Each ISL is 4 GFC (effective throughput = 3.5 Gb/s).
What is the total aggregated bandwidth between the two switches?

**Solution:**
```
Total bandwidth = 2 × 3.5 = 7 Gb/s aggregated bandwidth
```

---

## MODULE V – Business Continuity: Backup and Recovery

### Theory Questions

**Short Answer:**
1. Define RTO and RPO. How do they relate to backup strategy?
2. What are the three types of DR sites (hot, warm, cold)? Compare them.
3. What is a Business Impact Analysis (BIA)?
4. Define SPOF (Single Point of Failure). Give two examples in a data center.
5. What is fault tolerance? How is it implemented?
6. Differentiate between full, incremental, and cumulative backup.
7. What is Bare-Metal Recovery (BMR)?
8. What is a hot backup vs. a cold backup?
9. Explain the three backup topologies: Direct-attached, LAN-based, SAN-based.
10. What is a Virtual Tape Library (VTL)? What are its benefits?
11. Explain NDMP. How does it improve NAS backup?
12. What is serverless backup?
13. What is multipathing? How does EMC PowerPath help?
14. List the four load balancing policies in PowerPath.
15. Explain the BC Planning Lifecycle (5 stages).

**Long Answer:**
1. Explain the BC Planning Lifecycle with all five stages and their key activities.
2. Compare full, incremental, and cumulative backup. Include backup time, storage needed, and restore complexity.
3. Describe the three backup topologies. Draw a diagram for SAN-based (LAN-free) backup. What is the advantage of SAN-based backup?
4. A company's DB server has 4 paths to storage. Explain how multipathing software (PowerPath) manages path failover in an active-passive array.
5. Explain the concept of SPOF and fault tolerance in a storage network. How can all SPOFs in a simple Host-Switch-Storage configuration be eliminated?

---

### Numerical Problems (Module V)

**Q1.** A bank's currency table is available Mon-Fri, 9 AM to 4 PM (7 hrs/day, 5 days/week).
During one week, the table was unavailable for 40 minutes (9:05 AM to 9:45 AM) on Thursday, with additional 15 min verification time.
Calculate availability for the week.

**Solution:**
```
Total available time = 7 hrs × 5 days = 35 hours = 2100 minutes
Downtime = 40 + 15 = 55 minutes (unavailable during 9:05-9:45 and verification)
Availability = (2100 - 55)/2100 × 100 = 2045/2100 × 100 = 97.38%
```

---

**Q2.** Network router: failure rate = 0.02% per 1,000 hours. Calculate MTBF.

**Solution:**
```
Failure rate = 0.02/100 per 1000 hours = 0.0002/1000 per hour = 0.0000002 per hour
MTBF = 1/failure rate = 1/0.0000002 = 5,000,000 hours
```

---

**Q3.** A system has 100 GB total data. 10% changes daily.
Calculate weekly storage for each backup strategy (Full on Sunday + Daily backup Mon-Sat):

a. Full backup daily
b. Incremental backup
c. Cumulative (differential) backup

**Solution:**
```
a. Full daily: 100 GB × 7 = 700 GB/week

b. Incremental (Mon-Sat: 10 GB each day):
   100 (Sunday full) + 6 × 10 = 160 GB/week

c. Cumulative:
   Sun: 100 GB (full)
   Mon: 10 GB (since full)
   Tue: 20 GB (Mon+Tue changes)
   Wed: 30 GB (Mon+Tue+Wed)
   Thu: 40 GB
   Fri: 50 GB
   Sat: 60 GB
   Total: 100+10+20+30+40+50+60 = 310 GB/week
```

---

**Q4.** A system has 50 GB of data with 20% daily change rate.
Monday: Full backup. Tue-Fri: Incremental. Disaster on Friday morning.
What do you need to restore?

**Solution:**
```
To restore to Thursday state:
1. Monday full backup (50 GB)
2. Tuesday incremental (10 GB)
3. Wednesday incremental (10 GB)
4. Thursday incremental (10 GB)
Total restore size: 80 GB from 4 backup sets
```

---

**Q5.** MTBF of a component = 800 hours. MTTR = 4 hours.
a. Calculate availability.
b. How much downtime per year?

**Solution:**
```
a. Availability = MTBF/(MTBF+MTTR) = 800/(800+4) = 800/804 = 99.502%

b. Annual downtime = (1 - 0.99502) × 8760 = 0.00498 × 8760 = 43.6 hours
```

---

## MODULE VI – Large Storage Systems

### Theory Questions

**Short Answer:**
1. What is the difference between a storage model and a data model?
2. Explain the Cell Storage Model and Journal Storage Model.
3. What are the design goals of NFS? What are file handles?
4. Explain AFS architecture. What are Venus and Vice?
5. What is GPFS? What is a distributed lock manager?
6. Describe the GFS architecture. What is a chunk? What is the chunk size?
7. Why did Google use a single master in GFS? What is the risk?
8. Explain the GFS write process (5 steps).
9. What is HDFS? Compare HDFS components with GFS components.
10. What is MapReduce? Explain the Map, Shuffle, and Reduce phases.
11. What is BigTable? Describe its data model (row, column family, timestamp).
12. Explain the BigTable tablet hierarchy (root → metadata → user).
13. What is Megastore? How does it achieve multi-datacenter consistency?
14. What is Paxos? How is it used in Megastore?
15. What is Amazon S3? What is a Bucket? What is an Object?
16. Compare Amazon S3 with a traditional file system.
17. What is MVCC (Multi-Version Concurrency Control)? Where is it used?
18. What is the difference between GFS and HDFS in terms of consistency?
19. Explain Fair Scheduler vs Capacity Scheduler in Hadoop.
20. What makes BigTable "sparse, distributed, and persistent"?

**Long Answer:**
1. Explain the Google File System (GFS) in detail. Include architecture, chunk design, write process, consistency model, and comparison with traditional file systems.
2. Describe Hadoop architecture. Explain HDFS (NameNode, DataNodes, replication) and MapReduce (with word count example).
3. Compare the following distributed storage systems: NFS, AFS, GPFS, GFS, HDFS. Include scale, consistency, use case, and key features.
4. Describe BigTable's data model, architecture (master + tablet servers), and tablet hierarchy.
5. Explain Amazon S3. What are its features, limitations, and suitable use cases? Compare it with HDFS for big data analytics.

---

## Combined Formulas Reference

### Disk Performance
```
RS = E + L + X              (Disk Service Time)
E = Seek Time
L = (0.5 × 60000) / RPM    (Rotational latency in ms)
X = Block Size / Transfer Rate  (Internal transfer time)

IOPS = 1/RS

U = a × RS = RS/Ra          (Utilization)
R = RS/(1-U)                (Response time)
NQ = U²/(1-U)              (Average queue size)
Time in queue = U × R

N = Max(C, I)               (Total disks needed)
```

### RAID Disk Load
```
Disk load = (Read%) × App_IOPS + Write_penalty × (Write%) × App_IOPS

Write penalties:
  RAID 0 = 0 (no protection)
  RAID 1 = 2
  RAID 5 = 4
  RAID 6 = 6

Storage efficiency:
  RAID 0 = 100%
  RAID 1 = 50%
  RAID 5 = (n-1)/n × 100%
  RAID 6 = (n-2)/n × 100%
```

### Availability
```
Availability = (MTBF) / (MTBF + MTTR) × 100%
            = (Total time - Downtime) / Total time × 100%

99% (2-nines) → 87.6 hrs downtime/year
99.9% (3-nines) → 8.76 hrs/year
99.99% (4-nines) → 52.6 min/year
99.999% (5-nines) → 5.26 min/year
```

### MTBF of Array
```
Array MTBF = Single Disk MTBF / Number of Disks
```

---

## Important Definitions – Master Table

| Term | Module | Definition |
|------|--------|-----------|
| ILM | I | Proactive strategy for data management throughout its lifecycle |
| LBA | I | Linear address scheme replacing CHS; maps to physical sector |
| RAID | II | Redundant Array of Independent Disks; data protection and performance |
| Write Penalty | II | Extra I/Os per write due to RAID parity calculations |
| Hot Spare | II | Idle disk that automatically replaces failed disk |
| DAS | III | Direct-Attached Storage; storage connected directly to server |
| SAN | III | Storage Area Network; dedicated block-level storage network |
| NAS | III | Network-Attached Storage; file-level storage on LAN |
| WWN | III | World Wide Name; 64-bit unique identifier in FC environment |
| Zoning | III | SAN access control mechanism; defines which hosts see which storage |
| ISL | III | Inter-Switch Link; connects two FC switches |
| iSCSI | III | SCSI over TCP/IP; block storage over IP networks |
| FCIP | III | FC tunneled over IP WAN; extends SAN between sites |
| FC Director | IV | High-availability, chassis-based FC switch; 5-nines availability |
| LACP | IV | Link Aggregation Control Protocol; IEEE 802.3ad; combines links |
| Metro Ethernet | IV | Carrier Ethernet in metropolitan areas |
| InfiniBand | IV | High-speed, low-latency interconnect for HPC; uses RDMA |
| RDMA | IV | Remote Direct Memory Access; bypasses OS kernel |
| RTO | V | Recovery Time Objective; max acceptable time to restore service |
| RPO | V | Recovery Point Objective; max acceptable data loss |
| SPOF | V | Single Point of Failure; one component whose failure disrupts all |
| BIA | V | Business Impact Analysis; evaluates financial impact of disruption |
| BMR | V | Bare-Metal Recovery; complete system rebuild from backup |
| VTL | V | Virtual Tape Library; disk emulating tape for backup |
| NDMP | V | Network Data Management Protocol; NAS backup protocol |
| GFS | VI | Google File System; petabyte-scale, commodity hardware FS |
| HDFS | VI | Hadoop Distributed File System; open-source GFS equivalent |
| BigTable | VI | Google's distributed structured data store on GFS |
| Megastore | VI | Google's multi-datacenter storage with ACID transactions |
| MapReduce | VI | Parallel processing framework: Map → Shuffle → Reduce |
| Paxos | VI | Consensus algorithm for distributed systems |
| S3 | VI | Amazon's object storage; buckets + objects; flat namespace |
