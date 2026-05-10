# Module IV: Network Components
**Subject:** Information Storage Management (ISM)  
**Reference:** Module 4 PDF + Storage Networks Explained (Troppen et al.) + Book Ch. 6, 8

---

## Overview
This module covers the network connectivity components used in storage networks:
- **Switches and Directors** – core switching fabric devices
- **Highly Available Systems** – fault-tolerant network design
- **Fibre Channel** – primary SAN transport
- **1GE/10GE Ethernet** – Gigabit and 10 Gigabit Ethernet for storage
- **Metro Ethernet** – metropolitan area networks using Ethernet
- **Aggregation** – combining multiple network links/disks
- **InfiniBand** – high-performance computing interconnect

---

## 4.1 Connectivity: Switches, Directors, Highly Available Systems

### 4.1.1 Switches

**Switch** = a network device that connects multiple nodes and forwards data to specific ports.

**Key characteristics of FC Switches:**
- Operate at the fabric level; each port gets a dedicated connection.
- **Intelligent:** Can route frames via FSPF (Fabric Shortest Path First).
- Support **zoning** for access control.
- Support **non-blocking** fabric (multiple simultaneous communications).
- Can be cascaded via **ISL (Inter-Switch Links)** to build larger fabrics.

**Types of FC Switches:**
1. **Fixed-port switches:** Fixed number of ports; used in smaller environments.
2. **Modular switches:** Blade-based; scalable to hundreds of ports.

**Switch Functions:**
- Switching/routing FC frames.
- Fabric services: Name Server, Login Server, Time Server, Management Server.
- Zoning enforcement.
- Performance monitoring and management.

**Management:**
- Via **SNMP (Simple Network Management Protocol)** for integration with management software.
- Web GUI, CLI, or dedicated management software (e.g., EMC Connectrix Manager).

---

### 4.1.2 FC Directors

**FC Director** = High-end, highly available Fibre Channel switch with carrier-class availability.

**Key differences from regular switches:**
- **Chassis-based** with redundant components.
- Designed for **99.999% (five nines) availability** = less than 5.26 minutes downtime/year.
- **Non-disruptive upgrades** – add blades/components without downtime.
- Greater number of ports (128–512+ ports).

**Director components:**
- **Chassis/Frame:** Physical enclosure.
- **Core/Cross-connect:** High-speed backplane connecting all blades.
- **Port Blades:** Contain multiple FC ports.
- **Control Blade:** Manages fabric logic.
- **Power Supplies:** Redundant (N+1 or 2N).
- **Cooling:** Redundant fan modules.

**Purchase:** Usually through server vendor, storage vendor, systems integrator, or VAR.

---

### 4.1.3 Highly Available Systems

**High Availability (HA)** = system or component continuously operational for a desirably long time.

**Availability measurement:**
```
Availability = Uptime / (Uptime + Downtime) × 100%
```

**"Nines" of availability:**

| Availability | Nines | Annual Downtime |
|-------------|-------|----------------|
| 99% | 2-nines | 87.6 hours |
| 99.9% | 3-nines | 8.76 hours |
| 99.99% | 4-nines | 52.6 minutes |
| 99.999% | 5-nines | 5.26 minutes |
| 99.9999% | 6-nines | 31.5 seconds |

**HA mechanisms for storage networks:**
- **RAID:** Disk-level redundancy.
- **Dual power supplies** in switches and arrays.
- **Redundant paths** (multiple HBAs, multiple switches).
- **SAN:** Provides path redundancy through multiple fabric paths.
- **Clustering:** Multiple servers sharing same storage; heartbeat monitoring.
- **Multipathing software:** Auto-failover between paths.

**HA requires eliminating Single Points of Failure (SPOF):**
- Redundant HBAs on servers.
- Redundant fabrics (two separate SAN fabrics).
- Redundant storage array ports.
- Redundant WAN links.
- Battery-backed or UPS-protected power.

---

## 4.2 Fibre Channel (FC)

**Fibre Channel (FC)** = high-speed network technology providing lossless delivery of raw block data.

**Primary use:** Connecting computer data storage to servers in SANs.

**Key characteristics:**
- In-order, lossless frame delivery.
- Primarily used in data center SANs.
- Runs on optical fiber (within/between data centers) or copper (limited distance).
- FC network = "switched fabric" (operates as one big switch).

### FC Speeds:

| Standard | Speed |
|---------|-------|
| 1GFC | 1.0625 Gb/s |
| 2GFC | 2.125 Gb/s |
| 4GFC | 4.25 Gb/s |
| 8GFC | 8.5 Gb/s |
| 16GFC | 14.025 Gb/s |
| 32GFC | 28.05 Gb/s |

### FC Topologies (Revisited):

**1. Point-to-Point:**
- Two devices directly connected.
- Simplest topology; limited connectivity.

**2. Arbitrated Loop (FC-AL):**
- Devices in a ring/loop.
- Only one pair communicates at a time.
- Max speed: 8GFC.
- Rarely used after 2010.

**3. Switched Fabric (FC-SW):**
- All devices connected to FC switches.
- Scale to tens of thousands of ports.
- FSPF (Fabric Shortest Path First) for routing.
- Multiple pairs communicate simultaneously.
- Port failure isolated to that link only.

### FC Upper Layer Protocols (ULPs):
FC-4 layer carries various ULPs:
- **FCP (Fibre Channel Protocol):** SCSI commands over FC (most common).
- **FICON:** Mainframe storage protocol over FC.
- **NVMe-oF:** NVMe storage over FC fabrics (modern).
- **IP over FC:** IP traffic over FC networks.

### FC Frame Review:
```
Frame = SOF (4B) + Header (24B) + Data (0-2112B) + CRC (4B) + EOF (4B)
```
Max payload per frame: **2,112 bytes**.

---

## 4.3 1GE/10GE (Gigabit and 10 Gigabit Ethernet)

### Ethernet for Storage Networks

**1GE (1 Gigabit Ethernet):**
- Speed: **1 Gb/s** (125 MB/s).
- Uses IEEE 802.3z (fiber) or 802.3ab (copper/Cat5e).
- Used for iSCSI, NAS, and management traffic.
- Distance: up to 100 m (copper), up to 550 m (multimode fiber).

**10GE (10 Gigabit Ethernet):**
- Speed: **10 Gb/s** (1.25 GB/s).
- Uses IEEE 802.3ae (fiber) or 802.3an (copper/Cat6a).
- Used for high-performance iSCSI, NAS, data center backbones.
- **FCoE (Fibre Channel over Ethernet):** Run FC traffic over 10GE network.

**Benefits of 10GE for Storage:**
1. Higher throughput for large-scale storage networks.
2. Consolidation of SAN and LAN on single network infrastructure.
3. Lower cost vs FC (uses commodity Ethernet hardware).

**Standards:**
- 10GBASE-SR (short range, multimode fiber, 26-300 m)
- 10GBASE-LR (long range, singlemode fiber, 10 km)
- 10GBASE-T (copper, Cat6a, 55-100 m)

---

## 4.4 Metro Ethernet

**Metro Ethernet** = use of Carrier Ethernet technology in Metropolitan Area Networks (MANs).

**Definition:** Connects business LANs and end users to WAN or Internet across a city/metro area.

**Key features:**
- Collective endeavor with multiple financial contributors.
- Typically star or mesh network topology.
- Uses cable or fiber optic media.

### Benefits of Metro Ethernet:

| Benefit | Description |
|---------|-------------|
| **Flexibility** | Supports wide variety of services and transports |
| **Reliability** | OAM (Operations, Administration, Maintenance) – path discovery, failure detection |
| **Cost-effectiveness** | Less complicated than WAN; lower equipment and ownership costs |
| **QoS** | Supports classification, marking, policing, queuing, scheduling |
| **Scalability** | Speeds from 1 Mbps to 10 Gbps; dynamic bandwidth increase |

### Metro Ethernet vs Traditional WAN:
- **Traditional WAN:** SDH (Synchronous Digital Hierarchy) or MPLS – expensive, complex.
- **Metro Ethernet:** Simpler, cheaper; can apply MPLS on top for enterprise-grade services.

### Metro Ethernet Services:
1. **E-Line (Ethernet Private Line):** Point-to-point service between two sites.
2. **E-LAN (Ethernet LAN):** Multipoint service connecting multiple sites.
3. **E-Tree:** Hub-and-spoke connectivity (one root, multiple leaves).

### Use Cases:
- Connecting branch offices to corporate HQ.
- Enterprise campus connectivity.
- Data center interconnects within a metro area.
- Carrier Ethernet for ISP services.

---

## 4.5 Aggregation (Link Aggregation)

**Aggregation** = combining multiple network links into one logical link for increased bandwidth and redundancy.

**Also called:**
- **Link Aggregation Control Protocol (LACP)** – IEEE 802.3ad standard.
- **Port Channeling** (Cisco terminology).
- **Bonding** (Linux terminology).
- **Disk aggregation** = combining multiple disks (similar concept).

### Network Link Aggregation:

**Benefits:**
1. **Increased bandwidth:** 2 × 1GE links = 2 Gb/s aggregate bandwidth.
2. **Redundancy:** If one link fails, traffic continues on others.
3. **Load balancing:** Traffic distributed across all links.

**How it works:**
```
Switch A ──[Link 1]──┐
Switch A ──[Link 2]──┤──[Logical Trunk]──Switch B
Switch A ──[Link 3]──┘
```
Appears as one high-bandwidth connection to upper layers.

### Types of Load Balancing in Aggregation:
- **Round-robin:** Packets distributed sequentially.
- **Hash-based:** Source/destination IP or MAC-based load balancing.

### SAN Aggregation:
- Multiple ISLs between FC switches can be aggregated (port trunking in FC).
- Provides more bandwidth between switches.

---

## 4.6 InfiniBand (IB)

**InfiniBand** = computer-networking communications standard for high-performance computing (HPC).

**Key characteristics:**
- Very high throughput + very low latency.
- Used for data interconnect within and between computers.
- Used as storage interconnect (servers ↔ storage).
- Used as interconnect between storage systems.

### Why InfiniBand?

**Problem with traditional bus systems:**
- Parallel buses (32/64-bit) become bottlenecks as data increases.
- Inflexible; limited bandwidth.

**IB solution:**
- Uses **serial (bit-at-a-time)** transmission.
- Fewer pins → lower cost, better reliability.
- Multiple channels via multiplexing.

### InfiniBand Architecture:

**Components:**
- **HCA (Host Channel Adapter):** Installed in processor/server.
- **TCA (Target Channel Adapter):** Installed in peripheral/storage devices.
- **IB Switch:** Connects HCAs and TCAs.
- **IB Router:** Connects different IB subnets.

**Addressing:** Uses **IPv6** (virtually unlimited device expansion).

**Message types:**
- **RDMA (Remote Direct Memory Access) read/write.**
- **Channel send/receive messages.**
- **Reversible transaction-based operations.**
- **Multicast transmissions.**

### InfiniBand Speeds:

| Standard | Speed |
|---------|-------|
| SDR (Single Data Rate) | 10 Gb/s |
| DDR (Double Data Rate) | 20 Gb/s |
| QDR (Quad Data Rate) | 40 Gb/s |
| FDR (Fourteen Data Rate) | 56 Gb/s |
| EDR (Enhanced Data Rate) | 100 Gb/s |
| HDR (High Data Rate) | 200 Gb/s |

### RDMA (Remote Direct Memory Access):
- Data transferred directly to/from application memory.
- **Bypasses OS kernel** → zero-copy, lower latency.
- Used in: HPC clusters, high-performance storage (NVMe-oF, iWARP).

### InfiniBand vs Ethernet vs FC:

| Feature | InfiniBand | Fibre Channel | Ethernet (10GE) |
|---------|-----------|--------------|----------------|
| Latency | Very low (<1 µs) | Low (~2-5 µs) | Moderate (~10 µs) |
| Throughput | Very high (100 Gb/s+) | High (32 Gb/s) | High (10-100 Gb/s) |
| Primary use | HPC, storage | SANs | General networking, storage |
| Cost | High | High | Low-Moderate |
| Distance | Short (data center) | Medium (data center) | Wide range |

---

## Summary: Network Components Comparison

| Component | Protocol | Primary Use | Speed |
|-----------|---------|------------|-------|
| FC Switch | Fibre Channel | SAN switching | 8-32 GFC |
| FC Director | Fibre Channel | High-availability SAN | 8-32 GFC |
| Ethernet Switch (1GE) | Ethernet | NAS, iSCSI, LAN | 1 Gb/s |
| Ethernet Switch (10GE) | Ethernet | High-perf NAS, iSCSI, FCoE | 10 Gb/s |
| Metro Ethernet | Ethernet (Carrier) | MAN connectivity | 1 Mb/s – 10 Gb/s |
| InfiniBand | IB | HPC, high-performance storage | 10-200 Gb/s |

---

## Module 4 – Quick Revision Points

1. **Switch** = intelligent network device forwarding frames to specific ports.
2. **Director** = high-availability, chassis-based FC switch with 5-nines availability.
3. **High Availability** = continuous operation; measured in "nines" (99.999% = 5.26 min downtime/year).
4. **SPOF** = Single Point of Failure; eliminated through redundancy.
5. **FC** = Fibre Channel; lossless, in-order block data delivery; used in SANs.
6. **FC topologies:** Point-to-Point, FC-AL, FC-SW (switched fabric).
7. **1GE** = 1 Gb/s; **10GE** = 10 Gb/s; used for iSCSI and NAS.
8. **FCoE** = Fibre Channel over Ethernet (runs FC over 10GE).
9. **Metro Ethernet** = Ethernet in metro area; connects branches/campuses.
10. **Link Aggregation (LACP)** = combine multiple links → more bandwidth + redundancy.
11. **InfiniBand** = HPC interconnect; very low latency (<1 µs); uses RDMA.
12. **RDMA** = Remote Direct Memory Access; bypasses OS → zero-copy data transfer.
13. **FSPF** = Fabric Shortest Path First; routing protocol for FC networks.
14. **ISL** = Inter-Switch Link; connects two FC switches in a fabric.

---

## Exam-Ready Definitions

| Term | Definition |
|------|-----------|
| **FC Director** | High-end FC switch with carrier-class HA, redundant components |
| **Five Nines** | 99.999% availability = max 5.26 min downtime per year |
| **LACP** | Link Aggregation Control Protocol – IEEE 802.3ad |
| **Metro Ethernet** | Carrier Ethernet in metropolitan area networks |
| **InfiniBand** | Serial, high-speed, low-latency interconnect for HPC |
| **RDMA** | Remote Direct Memory Access – kernel bypass for data transfer |
| **FSPF** | Fabric Shortest Path First – FC routing protocol |
| **FCoE** | Fibre Channel over Ethernet – FC on 10GE infrastructure |
| **SNMP** | Simple Network Management Protocol – for switch monitoring |
| **NPIV** | N_Port ID Virtualization – multiple virtual FC ports on one physical port |
