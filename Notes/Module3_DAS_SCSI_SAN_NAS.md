# Module III: Direct-Attached Storage, SCSI, SAN, NAS
**Subject:** Information Storage Management (ISM)  
**Reference:** Somasundaram & Shrivastava, Ch. 5, 6, 7, 8

---

## Chapter 5: Direct-Attached Storage (DAS) and SCSI

### What is DAS?
**DAS (Direct-Attached Storage)** is an architecture where storage connects **directly to servers** without a network in between.

- Uses block-level access protocols.
- Examples: Internal HDD, tape libraries, directly connected external HDD packs.
- Suitable for: Small businesses, departments, workgroups not sharing data across enterprise.

---

## 5.1 Types of DAS

### 5.1.1 Internal DAS
- Storage device internally connected to host via serial or parallel bus.
- **Limitations:**
  - Physical bus has distance limitations.
  - Limited number of devices supported.
  - Occupies space inside host → difficult maintenance.

### 5.1.2 External DAS
- Server connects directly to external storage device.
- Communication over **SCSI or FC protocol**.
- **Advantages over Internal DAS:**
  - Overcomes distance limitations.
  - Overcomes device count limitations.
  - Centralized storage management.

```
[Host 1] ─┐
[Host 2] ─┤──[Storage Array]
[Host 3] ─┘
External DAS Architecture
```

---

## 5.2 DAS Benefits and Limitations

| Benefits | Limitations |
|---------|-------------|
| Lower initial investment | Does not scale well |
| Simple configuration, easy deployment | Limited ports → limited host connectivity |
| Managed using host-based tools | Limited bandwidth → restricted I/O |
| Fewer hardware/software elements | Distance limitations |
| No network latency → potentially outperforms SAN/NAS | Cannot easily re-allocate resources |
| - | Creates isolated "islands" of storage |

---

## 5.3 Disk Drive Interfaces

### 5.3.1 IDE/ATA (Integrated Device Electronics / Advanced Technology Attachment)
- Most popular interface on modern disks.
- **Standards:** ATA, EIDE, Ultra ATA, Ultra DMA/133 (133 MB/s max).
- Supports **2 devices per connector** (master-slave config).
- Low cost; moderate performance.
- **40-pin connector** for disks; **34-pin** for floppy.

### 5.3.2 SATA (Serial ATA)
- Serial version of ATA; **replaces parallel ATA**.
- Point-to-point connectivity up to **1 meter**.
- Data transfer speed: **150 MB/s** (enhanced to **600 MB/s**).
- Uses **low-voltage differential signaling (LVDS)** with 250 mV.
- **7-pin connector** (thin cable) vs 40-pin IDE.
- **Hot-pluggable** (can connect/remove while running).
- **Single-device per port** → eliminates cable sharing.

### 5.3.3 Parallel SCSI
- One of the oldest and most popular storage interfaces.
- Connects HDDs, tapes, scanners, printers to host.
- **Maximum 16 devices** on a single bus.
- Speeds evolved from **5 MB/s (SCSI-1)** to **320 MB/s (Ultra320)**.

### Comparison: IDE/ATA vs SCSI

| Feature | IDE/ATA | SCSI |
|---------|---------|------|
| Speed | 100–133 MB/s | 320 MB/s |
| Connectivity | Internal only | Internal and external |
| Cost | Low | Moderate to high |
| Hot-pluggable | No | Yes |
| Performance | Moderate to low | High |
| Max devices | 2 | 16 |

### SAS (Serial Attached SCSI)
- Evolution of SCSI beyond Ultra 320.
- Uses SCSI commands; pin compatible with SATA.
- Data transfer: **3 Gb/s (SAS 300)**.
- Supports dual porting, full-duplex.
- Preferred over SCSI in high-end servers.

### FC (Fibre Channel) Disks
- Use FC-AL topology; speeds up to **8.5 Gb/s (8 GFC)**.
- Extensively used in SAN; can also be used in DAS.

---

## 5.4 Introduction to Parallel SCSI

**History:** SASI (1981) → SCSI → ANSI standard (1986).

### SCSI Versions:

| Interface | Standard | Bus Width | Clock | Max Throughput | Max Devices |
|-----------|---------|-----------|-------|---------------|------------|
| SCSI-1 | SCSI-1 | 8-bit | 5 MHz | 5 MB/s | 8 |
| Fast SCSI | SCSI-2 | 8-bit | 10 MHz | 10 MB/s | 8 |
| Fast Wide SCSI | SCSI-2 | 16-bit | 10 MHz | 20 MB/s | 16 |
| Ultra SCSI | SCSI-3 | 8-bit | 20 MHz | 20 MB/s | 8 |
| Ultra Wide SCSI | SCSI-3 | 16-bit | 20 MHz | 40 MB/s | 16 |
| Ultra2 Wide SCSI | SCSI-3 SPI-2 | 16-bit | 40 MHz | 80 MB/s | 16 |
| Ultra3 SCSI | SCSI-3 SPI-3 | 16-bit | 40 MHz DDR | 160 MB/s | 16 |
| Ultra320 SCSI | SCSI-3 SPI-4 | 16-bit | 80 MHz DDR | 320 MB/s | 16 |
| Ultra640 SCSI | SCSI-3 SPI-5 | 16-bit | 160 MHz DDR | 640 MB/s | 16 |

### 5.4.3 SCSI-3 Architecture
Three major components:
1. **SCSI-3 command protocol** – Primary commands + device-specific commands.
2. **Transport layer protocols** – Rules for device communication.
3. **Physical layer interconnects** – Electrical signaling and data transfer modes.

![Figure 5-3: SCSI-3 standards architecture – command protocol, transport layer, physical layer](../Figures/Ch5%266/image.png)

### SCSI Client-Server Model (Initiator-Target)
- **SCSI Initiator:** Issues commands (e.g., HBA on server = initiator).
- **SCSI Target:** Executes commands (e.g., storage device = target).
- Target contains **Logical Units (LU)**, each with a **device server** and **task manager**.

![Figure 5-4: SCSI-3 client-server model – Initiator sends Device Service Request; Target responds via Logical Unit](../Figures/Ch5%266/image%20copy.png)

![Figure 5-5: SCSI device models with different port configurations – Initiator, Target, Combined, Target with Multiple Ports](../Figures/Ch5%266/image%20copy%202.png)

### 5.4.4 SCSI Addressing
- Each device on the SCSI bus is assigned a unique **SCSI ID** (0–15 for 16-device bus).
- Controller is usually ID **7** (highest priority).
- Devices are further addressed via **Logical Unit Numbers (LUNs)**.

### 5.5 SCSI Command Model (CDB)
- **CDB = Command Descriptor Block** – fixed-length data block sent with each SCSI command.
- Contains operation code, LBA, transfer length, control field.
- **Operation Code:** First byte of CDB identifying the specific command (e.g., READ, WRITE, INQUIRY).
- **Status:** Returned by target after command execution (GOOD, CHECK CONDITION, etc.).

---

## Chapter 6: Storage Area Networks (SAN)

### 6.1 What is SAN?
**SAN (Storage Area Network)** is a dedicated, high-performance storage network that provides block-level storage access.

![Figure 6-1: SAN implementation – multiple servers connecting through FC SAN to shared storage arrays](../Figures/Ch5%266/image%20copy%203.png)

- Fabric of interconnected storage devices and servers.
- Separates storage from servers → centralized, shareable storage.

**Evolution:** DAS limitations → SAN concept → Fibre Channel SAN → IP SAN.

---

## 6.2 The SAN and Its Evolution
- **Early DAS problems:** Fragmented storage, low utilization, difficult management, no scalability.
- **SAN solution:** Shared storage accessible by multiple servers; central management.
- **FC SAN:** Uses Fibre Channel protocol; high-performance, low latency.

---

## 6.3 Components of SAN

### 6.3.1 Node Ports
- **Host side:** HBA (Host Bus Adapter) ports.
- **Storage side:** Array front-end ports.
- **Switch side:** Various types of FC ports.

### 6.3.2 Cabling
- **Fiber optic cable:** Multimode (shorter distance) or singlemode (longer distance).
- **Copper cable:** Used in limited scenarios for cost savings.

### 6.3.3 Interconnect Devices
- **FC Hubs:** Simple devices; connect multiple FC devices in an arbitrated loop topology.
- **FC Switches:** Intelligent devices; provide dedicated connections; scalable fabric.
- **FC Directors:** High-end, high-port-count switches with 100% availability.

### 6.3.4 Storage Arrays
- Connected to the SAN fabric via front-end ports.
- Provides LUNs (Logical Unit Numbers) to hosts.

### 6.3.5 SAN Management Software
- Manages SAN fabric, zoning, LUN mapping, performance monitoring.

---

## 6.4 FC Connectivity (Topologies)

### 6.4.1 Point-to-Point
- Two devices connected directly.
- **Simplest topology**; limited connectivity.
- Used for dedicated server-to-storage connections.

### 6.4.2 Fibre Channel Arbitrated Loop (FC-AL)
- All devices in a **loop/ring** (like Token Ring).
- **Maximum 127 devices** on a loop.
- **Limitations:**
  - Adding/removing a device disrupts all loop activity.
  - One device communicates at a time (sequential access).
  - **Maximum speed: 8 GFC**.
  - Rarely used after 2010.

### 6.4.3 Fibre Channel Switched Fabric (FC-SW)
- All devices connected to FC **switches** (like modern Ethernet).
- **Advantages:**
  - Scales to **tens of thousands of ports**.
  - **FSPF (Fabric Shortest Path First)** for optimized routing.
  - Multiple port pairs communicate simultaneously.
  - Port failure isolated to a single link.
  - Better performance than FC-AL.

![Figure: Fibre Channel SAN Evolution – from FC Arbitrated Loop (SAN Islands) to Interconnected SANs to Enterprise Switched Fabric](../Figures/Ch5%266/image%20copy%204.png)

**Fabric topologies:**
- **Core-Edge:** Edge switches connect to core switches; scalable hierarchical design.
- **Mesh:** All switches interconnected; high redundancy but complex.

---

## 6.5 Fibre Channel Ports

![Figure 6-12: Fibre Channel ports – N_Port, NL_Port, F_Port, FL_Port, E_Port in a fabric with private loop, hub, and ISL](../Figures/Ch5%266/image%20copy%205.png)

| Port Type | Description |
|-----------|-------------|
| **N_port** | Node port; HBA or array port connected to fabric |
| **NL_port** | Node port supporting arbitrated loop (FC-AL) |
| **F_port** | Fabric port; switch port connecting to N_port |
| **FL_port** | Fabric loop port; connects loop to switch fabric |
| **E_port** | Expansion port; connects two FC switches (Inter-Switch Link = ISL) |
| **G_port** | Generic port; auto-determines if E_port or F_port |

---

## 6.6 Fibre Channel Architecture

### 6.6.1 FC Protocol Stack (5 Layers)

![Figure 6-13: Fibre Channel protocol stack – FC-0 (Physical), FC-1 (Encode/Decode), FC-2 (Framing/Flow), FC-4 (SCSI/IP/ATM)](../Figures/Ch5%266/image%20copy%206.png)

| Layer | Name | Description |
|-------|------|-------------|
| FC-4 | Upper Layer Protocol | Maps upper-layer protocols (SCSI, IP, HIPPI, ESCON, ATM) |
| FC-3 | Common Services | (Not implemented in practice) |
| FC-2 | Transport Layer | Framing, flow control, routing, fabric services |
| FC-1 | Transmission Protocol | Encode/decode, 8b/10b encoding, error control |
| FC-0 | Physical Interface | Raw bits, cables, connectors, optical/electrical parameters |

### 6.6.2 Fibre Channel Addressing
- **24-bit FC address** assigned dynamically when a port logs on to fabric.
- **N_port address structure:** Domain ID (8 bits) + Area ID (8 bits) + Port ID (8 bits).
- **Maximum addresses:** 239 domains × 256 areas × 256 ports = **15,663,104** FC addresses.
- **WWN (World Wide Name):** 64-bit unique identifier; static (like MAC address).
  - **WWNN:** World Wide Node Name (identifies device).
  - **WWPN:** World Wide Port Name (identifies specific port).

### 6.6.3 FC Frame Structure
```
┌─────┬──────────────┬───────────────┬─────┬─────┐
│ SOF │ Frame Header │  Data Field   │ CRC │ EOF │
│4 B  │   24 Bytes   │  0-2112 Bytes │ 4 B │ 4 B │
└─────┴──────────────┴───────────────┴─────┴─────┘
```

**Frame Header fields:** S_ID, D_ID, SEQ_ID, OX_ID, RX_ID, R_CTL, TYPE, F_CTL, etc.

### 6.6.4 Structure and Organization of FC Data
- **Frame** = fundamental unit (up to 2,112 bytes payload); analogous to a "word".
- **Sequence** = contiguous set of frames from one port to another; analogous to a "sentence".
- **Exchange** = set of information units managed by two N_ports; analogous to a "conversation".

### 6.6.5 Flow Control
- **BB_Credit (Buffer-to-Buffer):** Hardware-based; controls max frames in transit.
  - Transmitter counts free receiver buffers; stops if count = 0.
  - Receiver acknowledges via R_RDY primitive.
- **EE_Credit (End-to-End):** Affects class 1 and class 2 traffic.

### 6.6.6 Classes of Service

| Feature | Class 1 | Class 2 | Class 3 |
|---------|---------|---------|---------|
| Connection | Dedicated | Non-dedicated | Non-dedicated |
| Delivery | In-order | Order not guaranteed | Order not guaranteed |
| Acknowledgment | Yes | Yes | No |
| Multiplexing | No | Yes | Yes |

**Class 3 is most commonly used in SANs** (connectionless, no acknowledgment, best-effort).

---

## 6.7 Zoning

**Zoning** = Security mechanism in SAN to control which hosts can access which storage.

**Why zoning?**
- Without zoning, all devices in fabric can see each other.
- Zoning provides **isolation** and **access control**.

### Types of Zoning:
1. **Port-based (Hard) Zoning:** Zone defined by physical switch port numbers.
   - **Advantage:** More secure (port can only be in one zone).
   - **Disadvantage:** Less flexible (changing device changes zone config).

2. **WWN-based (Soft) Zoning:** Zone defined by WWNs of devices.
   - **Advantage:** More flexible (device can move ports without zone changes).
   - **Disadvantage:** Less secure (WWN can be spoofed).

**Zone Set:** Collection of zones; only one zone set active at a time on a switch.

---

## 6.8 Fibre Channel Login Types

1. **FLOGI (Fabric Login):** N_port logging into the fabric; gets 24-bit FC address.
2. **PLOGI (Port Login):** Two N_ports logging into each other for communication.
3. **PRLI (Process Login):** N_ports negotiate upper-layer protocol parameters (e.g., SCSI).

---

## 6.9 FC Topologies

### Core-Edge Fabric
```
Edge Switch ──ISL──┐
Edge Switch ──ISL──┤──[Core Switch]──ISL──┐
Edge Switch ──ISL──┘                      └─[Another Core]
```
- **Servers** connect to edge switches.
- **Storage** connects to edge switches or core.
- **ISLs** connect edge to core.
- Scalable; suitable for large environments.

### Mesh Topology
- All switches directly connected to each other.
- High redundancy; multiple paths.
- Complex to manage; expensive.

---

## Chapter 7: Network-Attached Storage (NAS)

### What is NAS?
**NAS (Network-Attached Storage)** is dedicated storage for **file serving** that connects to an existing IP network (LAN).

- Provides **file-level access** to heterogeneous clients.
- Unlike SAN (block-level), NAS provides file-level sharing.
- Multiple clients access shared files simultaneously.

---

## 7.1 General-Purpose Servers vs NAS Devices

![Figure 7-1: General purpose server vs NAS device – NAS optimized with only File System, OS, and Network layers](../Figures/Ch7%268/image.png)

| Feature | General-Purpose File Server | NAS Device |
|---------|---------------------------|------------|
| CPU overhead | High (OS + file serving + other tasks) | Optimized for file serving |
| Performance | Moderate | High (purpose-built) |
| Cost | Higher | Lower (specialized) |
| Scalability | Limited | Better |
| Management | Complex | Simplified |

---

## 7.2 Benefits of NAS

1. **Ease of deployment** – Connects to existing LAN infrastructure.
2. **File sharing** – Multiple protocols (NFS for UNIX, CIFS for Windows).
3. **Cost-effective** – Consolidated storage; better utilization.
4. **Centralized management** – Single point of control.
5. **Scalability** – Can add capacity without disrupting existing servers.

---

## 7.3 NAS File I/O

### 7.3.1 File Systems and Remote File Sharing
- NAS device hosts its own file system.
- Clients access files using network protocols (NFS, CIFS).

### 7.3.2 Accessing a File System
- Client sends request over network → NAS device processes → returns data.
- Uses **RPC (Remote Procedure Call)** or SMB protocols.

### 7.3.3 File Sharing
- **NFS** – UNIX/Linux clients.
- **CIFS/SMB** – Windows clients.
- Both can coexist on same NAS device (heterogeneous environment).

---

## 7.4 Components of NAS

![Figure 7-3: Components of NAS – UNIX (NFS) and Windows (CIFS) clients connecting via IP to NAS Head with Network Interface, NFS/CIFS, NAS OS, Storage Interface](../Figures/Ch7%268/image%20copy.png)

```
Clients (Windows/UNIX) ─── LAN ─── NAS Head ─── Storage
                                        │
                              (File System, OS)
```

- **NAS Head:** NAS controller with file system, OS, and network interfaces.
- **Storage:** Connected to NAS head (DAS, SAN, or integrated disks).
- **LAN:** Ethernet network connecting clients to NAS.

---

## 7.5 NAS Implementations

### 7.5.1 Integrated NAS
- Storage disks are **integrated** directly into the NAS device.
- All-in-one solution.
- Suitable for smaller environments.

### 7.5.2 Gateway NAS
- NAS head connects to a **separate storage array** (SAN-based).
- Provides file services while leveraging SAN benefits (scalability, management).
- Suitable for large enterprise environments.

### 7.5.3 Integrated NAS Connectivity
```
[Clients] ── [LAN] ── [NAS Device (Integrated Storage)]
```

![Figure 7-4: Integrated NAS connectivity – clients accessing via IP to Integrated NAS System](../Figures/Ch7%268/image%20copy%202.png)

### 7.5.4 Gateway NAS Connectivity
```
[Clients] ── [LAN] ── [NAS Head] ── [FC SAN] ── [Storage Array]
```

---

## 7.6 NAS File-Sharing Protocols

### 7.6.1 NFS (Network File System)
- **Origin:** Sun Microsystems; first widely used distributed file system.
- **Clients:** UNIX/Linux systems.
- Works via **RPC (Remote Procedure Call)**.
- **Stateless protocol** (server doesn't maintain client state in NFS v2/3).
- Uses **file handles** to identify remote files.
- **Versions:** NFSv2 (stateless), NFSv3 (improved performance), NFSv4 (stateful, secure).
- Supports multiple OS (UNIX, Linux, Windows with client).

### 7.6.2 CIFS (Common Internet File System)
- **Also known as:** SMB (Server Message Block).
- **Clients:** Windows systems (also Linux with Samba).
- Works over **TCP/IP (port 445)** or NetBIOS.
- **Stateful protocol** (server maintains session state).
- Supports: File and printer sharing, authentication (Active Directory integration).

### Comparison: NFS vs CIFS

| Feature | NFS | CIFS |
|---------|-----|------|
| Origin | Sun Microsystems | Microsoft |
| Primary OS | UNIX/Linux | Windows |
| Protocol | RPC/UDP/TCP | TCP/IP |
| State | Stateless (v2/v3) / Stateful (v4) | Stateful |
| Performance | Generally faster for UNIX | Generally faster for Windows |
| Security | Less secure (older versions) | More secure (Kerberos support) |

---

## 7.7 NAS I/O Operations

**Hosting and Accessing Files on NAS:**

![Figure 7-6: NAS I/O operation – Client → IP Network → NAS Device (NFS/CIFS) → Block I/O → Storage Array](../Figures/Ch7%268/image%20copy%203.png)

![Figure 7-7: NAS accessing via Directory Services – Clients → IP → NAS → Authentication request to Directory Server](../Figures/Ch7%268/image%20copy%204.png)

1. Client sends file request (NFS/CIFS) over LAN.
2. NAS head receives request; looks up its file system.
3. File system translates to block I/O to underlying storage.
4. Data retrieved from disk; returned to client.

---

## 7.8 Factors Affecting NAS Performance and Availability

| Factor | Impact |
|--------|--------|
| LAN bandwidth | Higher bandwidth → better performance |
| NAS head CPU/RAM | More resources → faster file processing |
| Storage performance | Faster disks → lower latency |
| Number of clients | More concurrent clients → higher load |
| Protocol overhead | NFS vs CIFS; compression; encryption |
| Network congestion | High traffic → increased latency |

---

## DAFS (Direct Access File System)

- **DAFS** = Direct Access File System – extension of NFS.
- Uses **RDMA (Remote Direct Memory Access)** to bypass the OS stack.
- Data transferred directly to application memory → lower latency.
- Requires RDMA-capable NICs (e.g., InfiniBand HCAs).
- Used in high-performance computing environments.

---

## Chapter 8: IP SAN (iSCSI and FCIP)

### 8.1 iSCSI (Internet SCSI)

**iSCSI** maps SCSI commands over **TCP/IP networks**.

- Enables **block-level storage** access over standard Ethernet/IP networks.
- Alternative to Fibre Channel; lower cost (uses existing LAN infrastructure).
- Suitable for SMEs and remote offices.

### 8.1.1 Components of iSCSI

| Component | Description |
|-----------|-------------|
| **iSCSI Initiator** | Client (server) sending SCSI commands over IP |
| **iSCSI Target** | Storage device receiving iSCSI commands |
| **iSNS (Internet Storage Name Service)** | Discovery and management of iSCSI devices |
| **TOE (TCP Offload Engine)** | Offloads TCP/IP processing from CPU (optional) |
| **iSCSI HBA** | Dedicated hardware for iSCSI (optional) |

### 8.1.2 iSCSI Host Connectivity
- **Software initiator:** iSCSI driver running on standard NIC (CPU-intensive).
- **TOE NIC:** Offloads TCP processing to dedicated hardware.
- **iSCSI HBA:** Dedicated hardware for iSCSI (best performance, highest cost).

### 8.1.3 Topologies for iSCSI Connectivity
1. **Dedicated iSCSI network** – Separate physical network for storage.
2. **Converged iSCSI network** – Shared with regular LAN traffic.

![Figure 8-3: Native and bridged iSCSI connectivity – (a) Native: iSCSI HBA direct to IP; (b) Bridged: via iSCSI Gateway to FC SAN](../Figures/Ch7%268/image%20copy%207.png)

### 8.1.4 iSCSI Protocol Stack
```
Application (SCSI)
      ↓
iSCSI Layer (encapsulates SCSI in PDUs)
      ↓
TCP Layer
      ↓
IP Layer
      ↓
Ethernet (1GE / 10GE)
```

### 8.1.5 iSCSI Discovery
Methods for discovering iSCSI targets:
1. **iSNS (Internet Storage Name Service)** – Centralized directory.
2. **SendTargets** – Initiator queries target directly.
3. **Static configuration** – Manual IP:Port configuration.

![Figure 8-5: Discovery using iSNS – iSCSI Initiators and Target registering with iSNS Server over IP](../Figures/Ch7%268/image%20copy%208.png)

### 8.1.6 iSCSI Names
- **IQN (iSCSI Qualified Name):** `iqn.yyyy-mm.com.domain:identifier`
  - Example: `iqn.2005-06.com.emc:storage.disk1`
- **EUI (Extended Unique Identifier):** `eui.{64-bit EUI-64}`

### 8.1.7 iSCSI Session
- **Discovery session:** Find available targets.
- **Normal operational session:** I/O data transfer.
- Session identified by ISID (initiator) and TSIH (target session identifier).

### 8.1.8 iSCSI PDU (Protocol Data Unit)
- iSCSI encapsulates SCSI commands in **PDUs** (Protocol Data Units).
- PDU types: SCSI Command PDU, Data-Out PDU, Data-In PDU, Login Request/Response.

### 8.1.9 Ordering and Numbering
- **CmdSN (Command Sequence Number)** – Ensures ordered delivery of commands.
- **DataSN** – Sequence number for data PDUs.

### 8.1.10 iSCSI Error Handling and Security
- **Error handling:** TCP provides reliable delivery; iSCSI adds error recovery layers.
- **Authentication:** CHAP (Challenge-Handshake Authentication Protocol).
- **Encryption:** IPSec for data-in-transit encryption.

---

## 8.2 FCIP (Fibre Channel over IP)

**FCIP** = Fibre Channel over IP; extends FC SANs over **WAN/IP networks**.

![Figure 8-1: Co-existence of FC and IP storage technologies – FCIP Gateway extending FC SAN to remote site over WAN; iSCSI bridge at corporate DC](../Figures/Ch7%268/image%20copy%205.png)

![Figure 8-2: iSCSI and FCIP implementation – (a) iSCSI: Server → IP → iSCSI Gateway → Storage; (b) FCIP: Server → FCIP Gateway → IP → FCIP Gateway → Storage](../Figures/Ch7%268/image%20copy%206.png)

- Not a new protocol; tunnels FC frames in IP packets.
- Enables **site-to-site SAN extension** over IP networks.
- Used for: Remote replication, SAN-to-SAN connectivity over WAN.
- **Components:** FCIP gateways at each site tunnel FC traffic.

```
Site A: [FC SAN] ── [FCIP Gateway] ──── WAN/IP ──── [FCIP Gateway] ── [FC SAN] :Site B
```

### 8.2.2 FCIP Performance and Security
- **Performance:** Affected by WAN latency, bandwidth, error rates.
- **Security:** IPSec encryption for secure WAN tunneling.

### FCIP vs iSCSI Comparison

| Feature | FCIP | iSCSI |
|---------|------|-------|
| Purpose | Extend FC SAN over WAN | Block storage over IP LAN/WAN |
| Protocol | FC over IP tunneling | SCSI over TCP/IP |
| Use case | Site-to-site FC extension | Replace FC in SME |
| Infrastructure | Requires FC at each end | Only needs IP network |
| Cost | Higher (FC+IP) | Lower (IP only) |

---

## Module 3 – Quick Revision Points

### DAS
1. **DAS** = Direct-Attached Storage; storage connected directly to server.
2. **Two types:** Internal DAS and External DAS.
3. **DAS limitations:** No scalability, limited ports, isolated storage islands.

### SCSI
4. **SCSI** = Small Computer System Interface; industry standard storage interface.
5. **Ultra320 SCSI:** 320 MB/s throughput; max 16 devices.
6. **SCSI addressing:** SCSI ID (0-15) + LUN.
7. **Initiator** = HBA (sends commands); **Target** = storage (executes commands).

### SAN
8. **SAN** = dedicated high-performance network for block-level storage.
9. **FC topologies:** Point-to-Point, FC-AL (loop), FC-SW (switched fabric).
10. **FC-AL** max devices: 127; **FC-SW** can scale to millions of addresses.
11. **Zoning:** Port-based (hard) vs WWN-based (soft).
12. **FC Protocol Stack:** FC-0 (Physical), FC-1 (Encode), FC-2 (Transport), FC-4 (ULP).
13. **FC Frame:** SOF + 24B Header + 0-2112B Data + CRC + EOF.
14. **WWN:** 64-bit unique identifier (static); FC address: 24-bit (dynamic).

### NAS
15. **NAS** = dedicated file serving via LAN; file-level access.
16. **NFS** = for UNIX/Linux; **CIFS/SMB** = for Windows.
17. **Integrated NAS** = storage built-in; **Gateway NAS** = head + external SAN.

### IP SAN
18. **iSCSI** = SCSI over TCP/IP; uses IQN naming.
19. **FCIP** = FC tunneled over IP; extends SAN across WAN.
20. **iSCSI discovery:** SendTargets, iSNS, static config.
