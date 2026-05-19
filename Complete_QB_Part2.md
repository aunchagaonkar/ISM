# Complete Question Bank with Detailed Answers (Modules 4 - 6)

## Module IV: Network Components

### Q1. What is network aggregation? Discuss its importance. Describe LACP.
**Answer:**
**Answer:**
**Link aggregation** combines two or more physical network interfaces into a single logical interface to provide higher throughput, load sharing, transparent failover, and scalability. It is commonly used to increase bandwidth and provide redundant network paths.

**Importance:**
- **Higher throughput:** multiple links appear as one logical pipe.
- **High availability:** loss of a member link redistributes traffic across remaining links.
- **Load sharing:** traffic is balanced across member links to utilize capacity.

**LACP (IEEE 802.3ad):** the Link Aggregation Control Protocol automates negotiation and maintenance of an aggregated link. Devices running LACP detect member failures and dynamically add or remove links from the bundle.

### Q2. Describe Metro-Ethernet architecture. How does it support enterprise connectivity over large geographical areas?
**Answer:**
**Metro Ethernet (Carrier Ethernet):** Uses standard Ethernet technologies to provide connectivity services across a metropolitan area network (MAN).
**Architecture & Connectivity:**
It leverages optical fiber rings maintained by service providers to deliver massive bandwidth. It replaces legacy, expensive WAN technologies (like ATM or Frame Relay) with familiar Ethernet interfaces.
- **E-Line (Point-to-Point):** Connects two enterprise locations as if they were on the same LAN segment. Perfect for Data Center to Disaster Recovery site replication.
- **E-LAN (Multipoint-to-Multipoint):** Connects multiple branch offices together, allowing them all to communicate as if they were plugged into a single giant Ethernet switch.
- **E-Tree (Point-to-Multipoint):** A central hub (HQ) communicates with multiple spokes (branches).

### Q3. Explain the role of switches and directors in storage networking.
**Answer:**
- **FC Switches:** Fixed-port devices used primarily at the edge of a SAN to connect host servers. They provide basic fabric services and route frames based on FC IDs. They are cost-effective but have a single point of failure (e.g., single power supply or backplane).
- **FC Directors:** Enterprise-class, modular, chassis-based devices used at the core of a SAN. They are designed for **"Five Nines" (99.999%) availability**. They have fully redundant components (control processors, power supplies, cooling fans). If a blade fails, it can be hot-swapped without bringing down the switch. They are used to connect storage arrays and edge switches in Core-Edge topologies.

### Q4. Compare Fibre Channel, Ethernet, and InfiniBand.
**Answer:**
| Feature | Fibre Channel (FC) | Ethernet (TCP/IP) | InfiniBand (IB) |
|---------|-------------------|-------------------|-----------------|
| **Primary Use** | High-end Block Storage (SAN) | LAN, File Sharing (NAS), Web | High-Performance Computing (HPC) |
| **Protocol** | SCSI over FC | TCP/IP | RDMA over IB |
| **Reliability** | Lossless (BB_Credit) | Best effort (retransmissions) | Lossless, extremely low latency |
| **Latency** | Low (microseconds) | High (due to TCP overhead) | Ultra-low (nanoseconds) |
| **Cost** | Very High (HBAs, switches) | Low (ubiquitous hardware) | High (Specialized HCAs) |

### Q5. Discuss high availability systems in data centers.
**Answer:**
High availability (HA) means a system remains accessible for a very high percentage of time, typically by eliminating single points of failure.

**Common HA mechanisms:**
- Redundant power supplies and UPS.
- Multiple network paths and dual switches.
- RAID for disk fault tolerance.
- Dual HBAs and multipathing software for storage access.
- Clustering and failover to alternate servers.

**Goal:** if one component fails, the service continues with minimal interruption and the failover is transparent to users.

### Q6. Explain Metro Ethernet and its applications.
**Answer:**
Metro Ethernet uses carrier Ethernet services across a metropolitan area network to connect enterprise sites with high bandwidth and lower cost than traditional WAN technologies.

**Common services:**
- **E-Line:** point-to-point connectivity, useful for data center to DR replication.
- **E-LAN:** multipoint connectivity for branch offices.
- **E-Tree:** hub-and-spoke connectivity for headquarters and branches.

**Applications:**
- Data center interconnect.
- Branch office connectivity.
- Campus and metro-area enterprise networks.
- Replication traffic between primary and disaster recovery sites.

### Q7. Design a network for a medium-scale data center.
**Answer:**
For a medium-scale data center, a practical design is a **redundant core-edge network**:

- **Edge layer:** access switches for servers, connected with link aggregation for bandwidth and failover.
- **Core layer:** two redundant core switches/directors to carry north-south traffic.
- **Storage network:** separate Fibre Channel SAN or iSCSI network for storage traffic.
- **Uplink redundancy:** dual uplinks from each edge switch to each core switch.
- **Management network:** isolated network for monitoring, provisioning, and security.
- **Metro links / WAN:** optional Metro Ethernet or MPLS links for remote replication and branch connectivity.

This design provides scalability, fault tolerance, and predictable traffic flows.

### Q8. Write short notes on aggregation and scalability in networks.
**Answer:**
**Aggregation** combines multiple physical links into one logical link, increasing throughput and providing redundancy. LACP is the standard protocol used for link aggregation.

**Scalability** is the ability of a network to grow without losing performance. It is improved by:
- Adding more links to an aggregation group.
- Adding more switches or higher-capacity switches.
- Using hierarchical topologies such as core-edge design.
- Separating traffic types into different networks or VLANs.

Together, aggregation and scalability help data centers absorb growth in users, applications, and storage traffic.

---

## Module V: Business Continuity & Backup

### Q1. What are the key components of a backup and restore operation in enterprise IT?
**Answer:**
**Answer:**
A backup system uses a client/server architecture with the following components (book wording):
- **Backup client:** software on production hosts that scans and sends data to be backed up.
- **Backup server:** coordinates backup operations, maintains the backup catalog (metadata and media locations), and manages schedules and policies.
- **Storage node (media server):** receives backup data from clients and writes it to the backup device.
- **Backup device:** the physical or virtual target (tape library, VTL, disk staging area) where backup data is stored.

The backup server instructs clients and storage nodes during a job, updates the catalog, and coordinates media mounting and restores.

![Backup Architecture](/Figures/Ch11,12,13,14/image%20copy%207.png)

### Q2. Discuss the Business Continuity Planning Life Cycle. Why is it essential?
**Answer:**
Business Continuity (BC) ensures that an organization can survive and continue operating during and after a disaster (fire, cyberattack, hardware failure).
**Lifecycle Stages:**
1. **Establishing Objectives:** Define scope, budget, policies, and assemble the BC team.
2. **Analyzing:** Conduct Business Impact Analysis (BIA) and risk assessments to determine critical processes.
3. **Designing & Developing:** Create backup strategies, design DR sites, and develop emergency response procedures.
4. **Implementing:** Purchase and configure redundant hardware, replication software, and DR facilities.
5. **Training, Testing, & Assessing:** Train staff, conduct mock disaster drills (failover testing), and continually update the plan.

![BC Planning Lifecycle](/Figures/Ch11,12,13,14/image.png)

### Q3. Differentiate between Information Availability, RTO, and RPO.
**Answer:**
- **Information Availability:** The percentage of time a system is accessible to users. (e.g., 99.999% uptime).
- **RTO (Recovery Time Objective):** The maximum acceptable time to restore an application *after* a failure occurs. It dictates the recovery strategy (e.g., if RTO is minutes, a Hot Site and synchronous replication are required; if days, tape backups are sufficient).
- **RPO (Recovery Point Objective):** The maximum acceptable amount of data loss, measured in time. It dictates the backup frequency. (e.g., if RPO is 1 hour, backups or snapshots must be taken every hour).

### Q4. Compare different backup methods (Full, Incremental, Cumulative/Differential).
**Answer:**
- **Full Backup:** Copies all selected data.
  - *Pros:* Fastest and easiest to restore (only 1 tape/file needed).
  - *Cons:* Takes the longest time to run; consumes the most storage capacity.
- **Incremental Backup:** Copies only the data that has changed since the *last backup* (full or incremental).
  - *Pros:* Fastest to back up; requires the least storage space daily.
  - *Cons:* Slowest to restore. A full restore requires the last Full backup PLUS every single incremental backup made since then.
- **Cumulative (Differential) Backup:** Copies all data changed since the *last FULL backup*.
  - *Pros:* Faster to restore than incremental (requires only the Full backup + the latest cumulative backup).
  - *Cons:* Backup size grows larger each day until the next full backup.

### Q5. Explain backup topologies: Direct-attached, LAN-based, and SAN-based.
**Answer:**
**Answer:**
- **Direct‑attached:** backup device attached directly to a server; simple but not shareable.
- **LAN‑based:** clients send data over the LAN to the backup server/storage node; easy to deploy but consumes LAN bandwidth.
- **SAN‑based (LAN‑free):** backup data moves over the Fibre Channel SAN directly to the tape/library or disk staging area; the LAN is used only for control/metadata. SAN‑based backup offloads data movement from the LAN and enables shared backup devices among many hosts.

![SAN-Based Backup Topology](/Figures/Ch11,12,13,14/image%20copy%209.png)

### Q6. Perform a Business Impact Analysis (BIA) for an IT company.
**Answer:**
A BIA identifies the business processes that are most critical and measures the impact if they are disrupted.

**Example IT company BIA:**
- **Critical processes:** customer support platform, source code repository, CI/CD pipeline, ERP/finance system, email, and identity services.
- **Impacts of failure:**
  - Support platform outage delays issue resolution and hurts SLA compliance.
  - Repository outage blocks development and releases.
  - ERP outage affects billing and payroll.
- **Recovery objectives:**
  - Customer support and identity systems: very low RTO/RPO.
  - Development systems: moderate RTO/RPO.
  - Archive/reporting systems: longer RTO/RPO.

The BIA helps decide which workloads need hot sites, replication, or backup-only protection.

### Q7. Case Study: Design a disaster recovery plan for a hospital.
**Answer:**
A hospital needs continuous access to patient records, lab systems, radiology images, and clinical applications.

**Recommended DR plan:**
- Use redundant primary systems with RAID, dual power, and multipathing.
- Replicate critical databases synchronously to a nearby DR site.
- Replicate noncritical records asynchronously to reduce bandwidth use.
- Keep immutable backups for compliance and recovery from ransomware.
- Define RTO/RPO by department: emergency systems require the fastest recovery; archive systems can tolerate longer downtime.
- Test failover regularly and train staff on emergency procedures.

**Priority systems:**
1. Electronic health records
2. Laboratory information system
3. Radiology / imaging archive
4. Billing and administration

The DR site should be able to take over quickly with minimal data loss.

### Q8. Explain backup and restore operations with examples.
**Answer:**
Backup and restore follow a client/server workflow.

**Backup example:**
- A production server runs a backup client.
- The backup server starts the job and updates the catalog.
- The storage node receives data and writes it to tape or disk.

**Restore example:**
- A file is accidentally deleted.
- The backup server searches the catalog for the correct backup copy.
- The storage node mounts the media and sends the file back to the client.

**Key idea:** the backup server manages metadata, while the storage node and device handle the actual data movement.

### Q9. Discuss backup solutions in NAS environments.
**Answer:**
NAS environments can be backed up in several ways:

- **Server-based backup:** data is sent through the application server; simple but causes extra load.
- **Serverless backup:** the backup/storage node reads NAS data directly; reduces application-server involvement.
- **NDMP 2-way:** the NAS head writes to locally attached tape while metadata is managed over the network.
- **NDMP 3-way:** the NAS head sends data to a remote tape library or backup device.

**Best choice:** NDMP is commonly preferred because it reduces backup traffic and is designed specifically for NAS backup and recovery.

---

## Module VI: Large Storage Systems

### Q1. Describe the architecture and key features of the Google File System (GFS).
**Answer:**
GFS is a scalable distributed file system designed by Google for large data-intensive applications running on cheap commodity hardware.
**Architecture:**
- **Single Master Node:** Maintains all filesystem metadata (namespace, access control, file-to-chunk mappings). It holds the entire metadata in RAM for speed.
- **Chunkservers:** Hundreds of commodity Linux servers that store the actual data.
- **Chunks:** Files are divided into massive **64 MB chunks** (much larger than standard 4KB blocks). Each chunk is replicated 3 times across different chunkservers for fault tolerance.
**Key Features:**
- **Fault Tolerance:** Achieved via 3x chunk replication and heartbeat monitoring by the Master. If a chunkserver dies, the Master directs the creation of new replicas from surviving copies.
- **High Throughput:** Designed for streaming reads and large sequential appends rather than random small writes. Clients cache metadata but read data directly from chunkservers, preventing the single Master from becoming a bottleneck.

### Q2. Describe the programming model of Hadoop (MapReduce).
**Answer:**
Hadoop is an open-source framework based on Google's MapReduce and GFS (HDFS). It enables distributed processing of massive datasets across clusters of computers.
**Programming Model (MapReduce):**
1. **Map Phase:** The input data is split into blocks. Worker nodes process these blocks in parallel. The Map function takes input key-value pairs and processes them to generate intermediate key-value pairs (e.g., counting word frequencies in a document).
2. **Shuffle and Sort Phase:** The framework automatically sorts the intermediate outputs by key and transfers them to the Reduce nodes.
3. **Reduce Phase:** The Reduce function aggregates, summarizes, or filters the intermediate data associated with the same key to produce the final output result.
**Benefit:** It brings the computation to the data (running code on the DataNodes) rather than moving massive amounts of data over the network to the code.

### Q3. Explain cloud storage with focus on Amazon S3 architecture.
**Answer:**
Amazon S3 (Simple Storage Service) is a highly scalable Object Storage service.
**Architecture:**
- **Flat Namespace:** Unlike traditional file systems with deep directory trees, S3 uses a flat structure. Data is stored as **Objects** inside **Buckets**.
- **Objects:** Consist of the actual data, highly customizable metadata (key-value pairs), and a globally unique Identifier (URL).
- **RESTful API:** Data is accessed entirely over the internet using standard HTTP protocols (PUT, GET, DELETE).
- **Eventual Consistency:** When an object is overwritten, it takes time for the change to propagate across all global data centers.
- **Use Cases:** Ideal for storing unstructured data like web assets, backup archives, and media files, but not suitable for transactional databases that require sub-millisecond block access.

### Q4. Compare File System vs. Database Systems, and explain FS+DB Convergence.
**Answer:**
- **File Systems (FS):** Excellent for storing large binary objects (videos, documents). They are hierarchical and optimized for streaming, but lack complex querying, indexing, and transactional ACID guarantees.
- **Database Systems (DB):** Excellent for structured data (tables, rows). They provide ACID transactions and complex SQL querying, but are terrible at storing massive BLOBs (Binary Large Objects) efficiently.

**FS + DB Convergence:**
Modern applications (like social media or Google Search) require the massive scalability of File Systems combined with the querying power of Databases.
Convergence systems integrate both. They store massive unstructured files in a distributed file system, while simultaneously keeping structured metadata and indices in a distributed database overlay.
**Example:** Google's **BigTable** (Database) is built directly on top of **GFS** (File System). Hadoop's **HBase** is built on top of **HDFS**. This allows petabytes of unstructured data to be queried using structured paradigms.

### Q5. Introduction to Hadoop: architecture and working.
**Answer:**
Hadoop is an open-source distributed framework for storage and parallel processing of very large datasets using commodity hardware.

**Architecture:**
- **HDFS (storage):** stores large files by splitting them into blocks and replicating them across DataNodes.
- **NameNode:** master node that manages metadata and block locations.
- **DataNodes:** worker nodes that store the actual blocks.
- **MapReduce:** processing model that executes computation near the data.

**Working:**
1. Input data is split into blocks.
2. Map tasks process blocks in parallel.
3. Shuffle and sort group intermediate results by key.
4. Reduce tasks aggregate the final output.

Hadoop is suitable for batch analytics, log processing, and large-scale data mining.

### Q6. Case Study: Big data storage using the Hadoop ecosystem.
**Answer:**
A big-data platform can be built using the Hadoop ecosystem as follows:

- **HDFS** stores raw data at scale.
- **MapReduce** or Spark performs batch processing.
- **HBase** stores structured, low-latency data on top of HDFS.
- **Hive** enables SQL-like analytics.
- **Zookeeper** coordinates distributed services.

**Use case:** sensor logs, clickstream data, or transaction archives can be stored in HDFS and analyzed in parallel by MapReduce jobs.

### Q7. Compare cloud storage vs traditional storage systems.
**Answer:**
| Feature | Traditional Storage | Cloud Storage |
|---|---|---|
| Ownership | Owned and managed on-premises | Consumed as a service |
| Scaling | Requires hardware expansion | Elastic and on-demand |
| Cost model | High upfront capital expense | Pay-as-you-go operational expense |
| Access | Local or enterprise network | Internet/API based |
| Best for | Low-latency local workloads | Unstructured data, backup, archival, sharing |

**Summary:** traditional storage gives more direct control and predictable latency, while cloud storage gives elasticity, global accessibility, and simpler scaling.

### Q8. Design a scalable storage solution for a social media platform.
**Answer:**
A social media platform needs high write throughput, fast content delivery, and scalable object and metadata storage.

**Recommended design:**
- **Object storage** for photos, videos, and posts.
- **Distributed database** for user profiles, likes, comments, and indices.
- **HDFS/Hadoop or similar distributed storage** for logs and analytics.
- **CDN** for media distribution.
- **Caching layer** for hot content and sessions.
- **Replication and sharding** for availability and scale.

This design separates unstructured media from structured metadata, allowing independent scaling of each layer.
