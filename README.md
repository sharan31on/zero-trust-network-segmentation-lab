# Enterprise Zero Trust Network Segmentation Lab

A hands-on network security lab built in **Cisco Packet Tracer** demonstrating **Zero Trust Architecture (ZTA)** and **least-privilege access control** principles using VLAN segmentation, 802.1Q Router-on-a-Stick (ROAS) inter-VLAN routing, and Cisco IOS Extended Access Control Lists (ACLs).

---

## Topology Overview

![Network Topology](images/topology.png)

The infrastructure consists of four isolated security zones connected to a Cisco 2960 Layer 2 Switch, routed through a single Cisco 2911 Router via an 802.1Q trunk link:

- **HR Department:** Confidential personnel records
- **Finance Department:** High-risk accounting systems
- **IT Department:** Administrative and support zone
- **Guest Network:** Untrusted external devices

---

## IP Addressing & VLAN Design

| Host / Zone | VLAN ID | Network Subnet | Host IP Address | Default Gateway | Switch Interface |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Trunk Uplink** | Native | N/A | N/A | N/A | FastEthernet0/1 |
| **HR-PC** | 10 | `192.168.10.0/24` | `192.168.10.10` | `192.168.10.1` | FastEthernet0/2 |
| **Finance-PC** | 20 | `192.168.20.0/24` | `192.168.20.10` | `192.168.20.1` | FastEthernet0/3 |
| **IT-PC** | 30 | `192.168.30.0/24` | `192.168.30.10` | `192.168.30.1` | FastEthernet0/4 |
| **Guest-PC** | 99 | `192.168.99.0/24` | `192.168.99.10` | `192.168.99.1` | FastEthernet0/5 |

---

## Zero Trust Security Policy

Traditional perimeter-based networks assume all internal devices are trustworthy. This project enforces a Zero Trust posture inside the perimeter:

1. **Lateral Movement Restriction:** HR cannot communicate directly with Finance, preventing cross-department pivoting during an endpoint compromise.
2. **Untrusted Guest Isolation:** The Guest network cannot access any internal company subnets (HR, Finance, or IT).
3. **Administrative Access:** The IT department retains full connectivity across internal zones for operations and troubleshooting.

---

## Cisco IOS Configurations

### 1. Switch Configuration (Cisco 2960)

```ios
enable
configure terminal

! Define VLAN Database
vlan 10
 name HR
exit
vlan 20
 name Finance
exit
vlan 30
 name IT
exit
vlan 99
 name Guest
exit

! Access Port Assignments
interface FastEthernet0/2
 switchport mode access
 switchport access vlan 10
 exit

interface FastEthernet0/3
 switchport mode access
 switchport access vlan 20
 exit

interface FastEthernet0/4
 switchport mode access
 switchport access vlan 30
 exit

interface FastEthernet0/5
 switchport mode access
 switchport access vlan 99
 exit

! 802.1Q Trunk Uplink to Router
interface FastEthernet0/1
 switchport mode trunk
 exit
