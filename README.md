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
# Day 17- Inter-VLAN Routing using 802.1Q Trunking & ROAS

## 📌 Project Overview
This project demonstrates the implementation of **Inter-VLAN Routing** across a multi-department enterprise topology using **Cisco Packet Tracer**. It covers VLAN segmentation, 802.1Q trunking, Native VLAN security configurations, and Router-on-a-Stick (ROAS) setups.

---

##  Network Topology & Subnetting
The network is divided into three distinct VLANs using VLSM subnetting (`10.0.0.0/24` block):

* **Engineering Dept (VLAN 10):** `10.0.0.0/26` (Usable Range: `10.0.0.1 - 10.0.0.62`)
* **Pharmacy Dept (VLAN 20):** `10.0.0.64/26` (Usable Range: `10.0.0.65 - 10.0.0.126`)
* **BBA Dept (VLAN 30):** `10.0.0.128/26` (Usable Range: `10.0.0.129 - 10.0.0.190`)

---

##  Step-by-Step Configuration Commands

### 1. Switch 1 (SW1) Configuration
Assigning access interfaces, creating VLANs, and configuring trunking:

```both
SW1> enable
SW1# configure terminal

! Create VLANs
SW1(config)# vlan 10
SW1(config-vlan)# name Engineering
SW1(config-vlan)# vlan 30
SW1(config-vlan)# name BBA
SW1(config-vlan)# exit

! Assign Access Ports
SW1(config)# interface range FastEthernet 0/1 - 2
SW1(config-if-range)# switchport mode access
SW1(config-if-range)# switchport access vlan 10

SW1(config)# interface range FastEthernet 0/3 - 4
SW1(config-if-range)# switchport mode access
SW1(config-if-range)# switchport access vlan 30
SW1(config-if-range)# exit

! Configure Trunk Port to SW2
SW1(config)# interface GigabitEthernet 0/1
SW1(config-if)# switchport trunk encapsulation dot1q
SW1(config-if)# switchport mode trunk
SW1(config-if)# switchport trunk allowed vlan 10,30
SW1(config-if)# switchport trunk native vlan 1001
SW1(config-if)# exit

2. Switch 2 (SW2) Configuration
Configuring access ports, trunk to SW1, trunk to Router (R1), and defining Native VLAN:
SW2> enable
SW2# configure terminal

! Create VLANs
SW2(config)# vlan 10
SW2(config-vlan)# name Engineering_Extended
SW2(config-vlan)# vlan 20
SW2(config-vlan)# name Pharmacy
SW2(config-vlan)# vlan 30
SW2(config-vlan)# name BBA
SW2(config-vlan)# exit

! Assign Access Ports
SW2(config)# interface range FastEthernet 0/1 - 2
SW2(config-if-range)# switchport mode access
SW2(config-if-range)# switchport access vlan 20

SW2(config)# interface range FastEthernet 0/3 - 4
SW2(config-if-range)# switchport mode access
SW2(config-if-range)# switchport access vlan 10
SW2(config-if-range)# exit

! Trunk Link to SW1
SW2(config)# interface GigabitEthernet 0/1
SW2(config-if)# switchport trunk encapsulation dot1q
SW2(config-if)# switchport mode trunk
SW2(config-if)# switchport trunk allowed vlan 10,30
SW2(config-if)# switchport trunk native vlan 1001

! Trunk Link to Router R1
SW2(config)# interface GigabitEthernet 0/2
SW2(config-if)# switchport trunk encapsulation dot1q
SW2(config-if)# switchport mode trunk
SW2(config-if)# switchport trunk allowed vlan 10,20,30
SW2(config-if)# switchport trunk native vlan 1001
SW2(config-if)# exit

3. Router 1 (R1) Configuration (Router-on-a-Stick)
Configuring logical sub-interfaces with 802.1Q encapsulation and assigning default gateways:
R1> enable
R1# configure terminal

! Enable Physical Interface
R1(config)# interface GigabitEthernet 0/0
R1(config-if)# no shutdown
R1(config-if)# exit

! Sub-interface for VLAN 10
R1(config)# interface GigabitEthernet 0/0.10
R1(config-subif)# encapsulation dot1Q 10
R1(config-subif)# ip address 10.0.0.62 255.255.255.192

! Sub-interface for VLAN 20
R1(config)# interface GigabitEthernet 0/0.20
R1(config-subif)# encapsulation dot1Q 20
R1(config-subif)# ip address 10.0.0.126 255.255.255.192

! Sub-interface for VLAN 30
R1(config)# interface GigabitEthernet 0/0.30
R1(config-subif)# encapsulation dot1Q 30
R1(config-subif)# ip address 10.0.0.190 255.255.255.192
R1(config-subif)# exit

 Verification & Testing
Trunk Verification: Run show interfaces trunk on switches to verify active VLANs and Native VLAN status.

Routing Table: Run show ip route on R1 to verify connected sub-interface subnets.

Ping Tests: Executed ICMP pings from PC1 (VLAN 10) to PC5 (VLAN 20) and PC3 (VLAN 30) to verify successful Inter-VLAN packet delivery via ROAS.

 Key Takeaways
Trunk Filtering: Restricting allowed VLANs on trunk links improves network security and reduces unnecessary broadcast domains.

Native VLAN Security: Changing the default Native VLAN (VLAN 1) to an unused ID prevents double-tagging/VLAN hopping attacks.

Sub-interface Mapping: ROAS enables scalable Inter-VLAN routing over a single physical link by utilizing 802.1Q encapsulation tagging.

 ---

## 🛠️ Tools & Technologies
* **Simulator:** Cisco Packet Tracer 9.0.1v
* **Core Competencies:** IPv4 Subnetting (FLSM/VLSM), Static Routing, PDU Inspection, CLI Configuration, Physical Layer Troubleshooting, VLAN-Trunk & ROAS
