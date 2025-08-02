# 📘 3-Node ISIS VXLAN Static VTEP Lab

## 📖 Overview
This lab is designed to emulate a simplified **service provider-style fabric** using **Nokia SR OS** routers to demonstrate **underlay routing with ISIS** and **VXLAN-based Layer 2 overlays**. 

The lab simulates a **3-node core**:
- **PE1 (Provider Edge)**
- **GW (Gateway node)**
- **PE2 (Provider Edge)**

The **underlay** runs **ISIS** for loopback and transport reachability, while the **overlay** leverages **VXLAN with static flood lists** to handle BUM traffic.

---

### 🗺️ Lab Topology

### 🔹 Nodes
- **PE1** – Loopback: `1.1.1.1`
- **PE2** – Loopback: `2.2.2.2`
- **GW** – Loopback: `3.3.3.3`

### 🔹 Underlay Links
- `PE1 ↔ GW` – 10.1.1.0/30  
- `PE2 ↔ GW` – 10.2.2.0/30  

### 🔹 VLAN Segments
- **VLAN 10** → Subnet `192.168.10.0/24`  
- **VLAN 20** → Subnet `192.168.20.0/24`  
- **VLAN 30** → Subnet `192.168.30.0/24` (shared across all nodes)  

### 🔹 VXLAN VTEP Flood Lists
Each node maintains a **static flood list** of remote VTEPs for each VXLAN segment, ensuring BUM traffic replication across the fabric without multicast dependency.

## 📊 VLAN-to-VXLAN Mapping (Reference)

| VLAN| Subnet           | VNI| Flood List (VTEPs)    |
|-----|------------------|----|-----------------------|
| 10  | 192.168.10.0/24  | 10 | 1.1.1.1, 2.2.2.2      |
| 20  | 192.168.20.0/24  | 20 | 1.1.1.1, 2.2.2.2      |
| 30  | 192.168.30.0/24  | 30 | 1.1.1.1, 2.2.2.2, 3.3.3.3 |
---

## 🎯 Lab Objectives

This lab allows engineers and learners to:
- ✅ Establish **ISIS adjacencies** between all nodes.
- ✅ Verify **underlay reachability** (loopbacks and link networks).
- ✅ Configure and verify **VXLAN tunnels** between PEs.
- ✅ Use **static flood lists** to ensure proper BUM traffic handling.
- ✅ Confirm **end-to-end host connectivity** across extended VLAN segments.

---

## 📐 Lab Design Principles

- **Underlay**: ISIS Level 1-2 is used for simplicity and quick convergence in the lab setting.
- **Overlay**: VXLAN is configured with **static flood lists** rather than multicast for replication simplicity.
- **Scalability Concept**: While this lab demonstrates only three nodes, the principles scale to larger fabrics.
- **Isolation**: VLAN-to-VXLAN mappings isolate tenant traffic for clear segmentation.

---

## 🖥️ Verification & Validation Commands (SR OS)

Use these **commands on each SR OS node** to validate your configuration.

### 🔍 1. System Health
```bash
show system information
show version
```
Verifies software version, uptime, and platform health.

### 🔍 2. Interfaces & IP Status
```bash
show router interface
show router arp
```
Confirms interface status (Up/Down) and ARP resolution for attached subnets.

### 🔍 3. ISIS Underlay
```bash
show router isis adjacency
show router isis database
show router route-table
```
Ensures all nodes have full adjacencies and correct underlay routes.

### 🔍 4. VXLAN Services
```bash

show service id <vxlan-service-id> destinations
show service id <vxlan-service-id> detail

```
Validates VXLAN service state, bindings, and endpoints.

### 🔍 5. VXLAN Tunnel 
```bash
From gw-pe:
show service vxlan 1.1.1.1
show service vxlan 2.2.2.2
```
Checks if VTEPs are properly configured and present in the static flood list.

### 🔍 6. End-to-End Connectivity
```bash
log into Client 1 by: ssh root@client1
ping 192.168.10.2 
ping 192.168.20.2 
ping 192.168.30.3 
```
Confirms that hosts in the same VLAN can reach each other across the VXLAN overlay.

### 🔍 7. Optional Troubleshooting
```bash
show log log-id 99
show service fdb-mac
```

