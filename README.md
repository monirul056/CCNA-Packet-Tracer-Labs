# 🚀 Cisco CCNA 200-301 Hands-on Practice Labs

Welcome to my CCNA lab portfolio! This repository contains my completed hands-on network topologies, configuration files (.pkt), and troubleshooting practice using **Cisco Packet Tracer**, built while following the **Jeremy's IT Lab** curriculum.

---

## 📂 Lab Index & Topics Covered

### 1.Day-8 Interface Configuration & Basic Routing Setup
* **Concepts:** Basic IOS CLI, Subnetting, Interface Details (Speed/Duplex/Description), Device Hardening (Disabling Unused Ports), Configuration Persistence (copy running-config startup-config).
* **Networks Configured:**15.0.0.0/8, 182.98.0.0/16, 201.191.20.0/24.
* **Key Lessons & Troubleshooting:**
  * Resolved inter-subnet ping failure caused by a physical port mismatch (cabled to G0/2 instead of G0/1).
  * Analyzed initial ICMP timeout caused by ARP latency.
  * Ensured NVRAM memory persistence to retain setup post-reboot.

---

### 2. Day-11 Static Routing Fundamentals & Routing Table Analysis
* **Concepts:** Static Routes (ip route), Next-Hop IP vs. Exit Interface, Administrative Distance (AD), Routing Table Types (C, L, S).
* **Key Tasks:**
  * Configured static routing across a multi-router topology to connect isolated LAN segments.
  * Verified routing table entries using show ip route.
  * Tracked ICMP packet resolution and initial ARP address request behavior using Packet Tracer Simulation Mode.

---

### 3. Day-12 Life of a Packet & Multi-Router Static Routing
* **Concepts:** Two-way Static Routing, Packet Encapsulation/Decapsulation, Layer 2 vs. Layer 3 Header Modifications, TTL Management.
* **Key Tasks & Verification:**
  * Established full two-way reachability across intermediate routers.
  * Tracked end-to-end packet flow in Simulation Mode, verifying:
    * **IP Consistency:** Source & Destination IPs remain unchanged across the path.
    * **Hop-by-Hop MAC Rewriting:** Layer 2 source/destination MAC addresses update at each router hop.
    * **TTL Decrement:** TTL decreases by 1 at each layer-3 hop to mitigate routing loops.

---

### 4. Day-15 Advanced Subnetting (VLSM) & Topology Allocation
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




# Dayb 18 -  Layer 3 Switching, SVIs & Internet Routing

## 📌 Project Overview
This project demonstrates the transition from a traditional Router-on-a-Stick (ROAS) topology to a highly efficient **Layer 3 Switching (Multilayer Switching)** architecture using **Cisco Packet Tracer**. Furthermore, this lab includes a complete end-to-end setup to an external ISP router to simulate real-world internet connectivity from local VLANs.

##  Network Topology & Addressing
The network is segmented into three local VLANs, a point-to-point Layer 3 link to the Edge Router, and an external simulated Internet connection.

* **VLAN 10 (Engineering):** `10.0.0.0/26`
* **VLAN 20 (Pharmacy):** `10.0.0.64/26`
* **VLAN 30 (BBA):** `10.0.0.128/26`
* **L3 Link (SW2 to R1):** `10.0.0.192/30`
* **WAN Link (R1 to ISP):** `200.1.1.0/30`
* **Internet Cloud (Loopback):** `1.1.1.1/32`

##  Step-by-Step Configuration Commands
### 1. Multilayer Switch (SW2) Configuration
Configuring IP routing, Routed Ports, Switch Virtual Interfaces (SVIs) as default gateways, and a default route to the edge router.
SW2> enable
SW2# configure terminal

! Enable IPv4 Routing on the Switch
SW2(config)# ip routing

! Configure Routed Port towards Edge Router (R1)
SW2(config)# interface GigabitEthernet 1/0/2
SW2(config-if)# no switchport
SW2(config-if)# ip address 10.0.0.193 255.255.255.252
SW2(config-if)# no shutdown
SW2(config-if)# exit

! Configure SVIs (Default Gateways for VLANs)
SW2(config)# interface vlan 10
SW2(config-if)# ip address 10.0.0.62 255.255.255.192
SW2(config-if)# no shutdown

SW2(config)# interface vlan 20
SW2(config-if)# ip address 10.0.0.126 255.255.255.192
SW2(config-if)# no shutdown

SW2(config)# interface vlan 30
SW2(config-if)# ip address 10.0.0.190 255.255.255.192
SW2(config-if)# no shutdown
SW2(config-if)# exit

! Default Route to Edge Router
SW2(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.194
2. Edge Router (R1) Configuration
Connecting the local LAN to the external ISP, including default and static routing.
R1> enable
R1# configure terminal

! Interface connected to Multilayer Switch (SW2)
R1(config)# interface GigabitEthernet 0/0
R1(config-if)# ip address 10.0.0.194 255.255.255.252
R1(config-if)# no shutdown
R1(config-if)# exit

! Interface connected to ISP Router
R1(config)# interface GigabitEthernet 0/1
R1(config-if)# ip address 200.1.1.1 255.255.255.252
R1(config-if)# no shutdown
R1(config-if)# exit

! Default route towards the Internet (ISP)
R1(config)# ip route 0.0.0.0 0.0.0.0 200.1.1.2

! Static route to send return traffic back to local VLANs
R1(config)# ip route 10.0.0.0 255.255.255.0 10.0.0.193
3. ISP (Internet) Router Configuration
Simulating the external internet environment using a Loopback interface and providing a return route to the enterprise LAN.
ISP> enable
ISP# configure terminal

! Interface connected to Edge Router (R1)
ISP(config)# interface GigabitEthernet 0/0
ISP(config-if)# ip address 200.1.1.2 255.255.255.252
ISP(config-if)# no shutdown
ISP(config-if)# exit

! Simulating an Internet IP (e.g., Cloud DNS)
ISP(config)# interface loopback 0
ISP(config-if)# ip address 1.1.1.1 255.255.255.255
ISP(config-if)# exit

! Return route to the enterprise network (LAN)
ISP(config)# ip route 10.0.0.0 255.255.255.0 200.1.1.1
 Verification & Testing
SVI Status: Executed show ip interface brief on SW2 to ensure all VLAN SVIs are up/up.
Routing Tables: Verified routing tables using show ip route on SW2, R1, and ISP routers to confirm connected, local, and static routes.
Inter-VLAN Connectivity: Successfully pinged from PC (VLAN 10) to PC (VLAN 30), confirming L3 Switch internal routing.
Internet Connectivity: Successfully executed ping 1.1.1.1 from local PCs, proving complete end-to-end packet delivery and return from the ISP loopback interface.
 Key Takeaways
L3 Switching Efficiency: Replacing ROAS with SVIs on a Multilayer Switch significantly optimizes Inter-VLAN routing by avoiding bottleneck single-link trunk connections.
Routed Ports: A no switchport command effectively converts a Layer 2 switchport into a fully functional Layer 3 routed interface.
IP Routing Prerequisite: By default, Layer 3 switches act as Layer 2 devices. The ip routing global configuration command is mandatory to enable the routing engine.
End-to-End Routing Logic: Ensuring successful external communication requires configuring both outbound default routes and inbound static return routes across all participating L3 devices.


 ---



 # Day-21 Spanning Tree Protocol (STP) & PVST+ Configuration Lab

## 📌 Project Overview
This repository contains a practical Cisco Packet Tracer lab focused on **Spanning Tree Protocol (STP)** and **PVST+ (Per-VLAN Spanning Tree Plus)**. The objective of this lab is to demonstrate how to prevent Layer 2 loops, optimize network traffic through load balancing, manipulate STP path selection, and secure access ports.

## 🏗️ Network Topology
* **Switches:** 4x Cisco Catalyst Switches (SW1, SW2, SW3, SW4)
* **End Devices:** 2x PCs (PC1 in VLAN 1, PC2 in VLAN 2)
* **Connections:** Redundant trunk links between switches to simulate potential Layer 2 loops.

## 🎯 Lab Objectives & Tasks Completed
This lab covers the following core STP configurations:

1. **Verify Default STP State:** 
   * Analyzed the default Root Bridge election and identified Root, Designated, and Blocking ports.
2. **PVST+ Load Balancing:** 
   * Configured **SW1** as the Primary Root Bridge for VLAN 1 and Secondary for VLAN 2.
   * Configured **SW2** as the Primary Root Bridge for VLAN 2 and Secondary for VLAN 1.
   * *Result:* Effectively utilized all redundant physical links by dividing VLAN traffic.
3. **STP Path Cost Manipulation:** 
   * Increased the VLAN 1 cost of SW4's F0/2 interface to `100`.
   * *Result:* Forced SW4 to calculate a new path and select a different Root Port.
4. **STP Port Priority Manipulation:** 
   * Increased the VLAN 1 port priority of SW1's F0/1 interface to `240`.
   * *Result:* Forced downstream switches to prefer an alternate path due to the inferior priority.
5. **STP Edge Port Security:** 
   * Configured **PortFast** on access ports (F0/3 on SW3 and SW4) to bypass listening/learning states for immediate forwarding.
   * Configured **BPDU Guard** on the same access ports to protect the STP topology from unauthorized switches (err-disable on BPDU receipt).

## 🛠️ Commands Used
Here are some of the key Cisco IOS commands practiced in this lab:
```text
# Root Bridge Configuration
SW1(config)# spanning-tree vlan 1 root primary
SW1(config)# spanning-tree vlan 2 root secondary

# Path Cost Manipulation
SW4(config-if)# spanning-tree vlan 1 cost 100

# Port Priority Manipulation
SW1(config-if)# spanning-tree vlan 1 port-priority 240

# Security Features
SW3(config-if)# spanning-tree portfast
SW3(config-if)# spanning-tree bpduguard enable

# Verification
SW1# show spanning-tree
SW1# show spanning-tree vlan 1
🚀 How to Use This Lab
Download the STP_PVST_Lab.pkt file from this repository.

Open the file using Cisco Packet Tracer.

Access the CLI of any switch and use the show spanning-tree command to observe the current port states and Root Bridge status.
 --- 



## 🛠️ Tools & Technologies
* **Simulator:** Cisco Packet Tracer 9.0.1v
* **Core Competencies:** IPv4 Subnetting (FLSM/VLSM), Static Routing, PDU Inspection, CLI Configuration, Physical Layer Troubleshooting, VLAN-Trunk & ROAS,STP & PVST+
