# 🚀 Cisco CCNA 200-301 Hands-on Practice Labs

Welcome to my CCNA lab portfolio! This repository contains my completed hands-on network topologies, configuration files (.pkt), and troubleshooting practice using **Cisco Packet Tracer**, built while following the **Jeremy's IT Lab** curriculum.

---

## 📂 Lab Index & Topics Covered

### 1. - Interface Configuration & Basic Routing Setup
* **Concepts:** Basic IOS CLI, Subnetting, Interface Details (Speed/Duplex/Description), Device Hardening (Disabling Unused Ports), Configuration Persistence (copy running-config startup-config).
* **Networks Configured:**15.0.0.0/8, 182.98.0.0/16, 201.191.20.0/24.
* **Key Lessons & Troubleshooting:**
  * Resolved inter-subnet ping failure caused by a physical port mismatch (cabled to G0/2 instead of G0/1).
  * Analyzed initial ICMP timeout caused by ARP latency.
  * Ensured NVRAM memory persistence to retain setup post-reboot.

---

### 2.  - Static Routing Fundamentals & Routing Table Analysis
* **Concepts:** Static Routes (ip route), Next-Hop IP vs. Exit Interface, Administrative Distance (AD), Routing Table Types (C, L, S).
* **Key Tasks:**
  * Configured static routing across a multi-router topology to connect isolated LAN segments.
  * Verified routing table entries using show ip route.
  * Tracked ICMP packet resolution and initial ARP address request behavior using Packet Tracer Simulation Mode.

---

### 3. - Life of a Packet & Multi-Router Static Routing
* **Concepts:** Two-way Static Routing, Packet Encapsulation/Decapsulation, Layer 2 vs. Layer 3 Header Modifications, TTL Management.
* **Key Tasks & Verification:**
  * Established full two-way reachability across intermediate routers.
  * Tracked end-to-end packet flow in Simulation Mode, verifying:
    * **IP Consistency:** Source & Destination IPs remain unchanged across the path.
    * **Hop-by-Hop MAC Rewriting:** Layer 2 source/destination MAC addresses update at each router hop.
    * **TTL Decrement:** TTL decreases by 1 at each layer-3 hop to mitigate routing loops.

---

### 4. - Advanced Subnetting (VLSM) & Topology Allocation
* **Concepts:** Variable Length Subnet Masking (VLSM), Efficient IP Allocation, Gateway Configuration, Multi-LAN Static Routing.
* **Base Network:** `192.168.5.0/24`
* **Subnet Allocations:**
  * **LAN 2 (64 Hosts):** 192.168.5.0/25
  * **LAN 1 (45 Hosts):** 192.168.5.128/26
  * **LAN 3 (14 Hosts):** 192.168.5.192/28
  * **LAN 4 (9 Hosts):** 192.168.5.208/28
  * **WAN Link (2 Hosts):** 192.168.5.224/30 *(Standard P2P allocation; RFC 3021 /31 noted)*
* **Key Task:** Allocated IP blocks based on host requirements without address space waste, configured router interfaces, and verified end-to-end ICMP reachability across all 4 LANs.

---


### 5. Day 16 - VLAN Fundamentals & Broadcast Domain Isolation
* **Concepts:** Virtual LANs (VLANs), Layer 2 Broadcast Domain Isolation, Access Ports (`switchport mode access`), Access VLAN Assignment.
* **Key Tasks:**
  * Created and named 3 distinct VLANs across network departments:
    * **VLAN 10 (Engineering):** `10.0.0.0/26`
    * **VLAN 20 (HR):** `10.0.0.64/26`
    * **VLAN 30 (Sales):** `10.0.0.128/26`
  * Assigned switch ports to respective access VLANs on Cisco Catalyst 2960 Switch.
  * **Hardware Workaround:** Utilized FastEthernet port (`F0/13`) alongside Gigabit ports (`G0/1`, `G0/2`) to establish 3 physical inter-VLAN routing connections to Router R1.
  * **Verification & Testing:**
    * Achieved 100% end-to-end ICMP reachability (`PC1` to `PC9`) via Inter-VLAN routing.
    * Tested broadcast address (`10.0.0.62`) behavior to verify that Layer 2 broadcast traffic is strictly contained within its assigned VLAN and does not cross the router boundary.

 ---


## 🛠️ Tools & Technologies
* **Simulator:** Cisco Packet Tracer 9.0.1v
* **Core Competencies:** IPv4 Subnetting (FLSM/VLSM), Static Routing, PDU Inspection, CLI Configuration, Physical Layer Troubleshooting, VLAN
