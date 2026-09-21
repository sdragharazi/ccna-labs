# CCNA 200-301 & CompTIA Network+ — Lab Documentation

Hands-on lab notes, configurations and troubleshooting scenarios while studying for
**CCNA 200-301 (v2.0)** and **CompTIA Network+ (N10-009)**.

All labs are built and verified on both virtual and physical Cisco equipment.

---

## Environment

### Virtual
- **PNETLab** — Cisco IOL (L2/L3), vIOS, CSR1000v, MikroTik CHR
- **Cisco Packet Tracer**

### Physical
| Hostname | Device | Role |
|---|---|---|
| `SW-ACCESS` | 2x Cisco Catalyst 2960X (FlexStack) | Access Layer — L2 |
| `SW-CORE` | Cisco Catalyst 3750G | Distribution / Layer 3 |
| `RTR-EDGE` | MikroTik (RouterOS) | Internet Edge / NAT |

---

## Repository Structure

```
ccna-labs/
├── README.md
├── notes/           # Study notes per topic
├── labs/            # Lab scenarios, configs and outputs
│   └── lab-XX-name/
│       ├── README.md
│       └── outputs/
├── cheatsheets/     # Quick reference sheets
└── topologies/      # Network diagrams
```

Each lab includes: **Objective → Topology → IP Addressing Table → Configuration → Verification Output → Troubleshooting Notes**.

---

## Topics

| # | Topic | Notes | Lab | Status |
|---|---|---|---|---|
| 01 | Network Fundamentals | | | In progress |
| 02 | OSI Model & Encapsulation | | | Not started |
| 03 | Ethernet & Switching | | | Not started |
| 04 | ARP & MAC Address Table | | | Not started |
| 05 | IPv4 Addressing | | | Not started |
| 06 | Subnetting & VLSM | | | Not started |
| 07 | Cisco IOS Fundamentals | | | Not started |
| 08 | VLAN & Trunking | | | Not started |
| 09 | Inter-VLAN Routing | | | Not started |
| 10 | STP & RSTP | | | Not started |
| 11 | EtherChannel | | | Not started |
| 12 | Routing Table & Administrative Distance | | | Not started |
| 13 | Static & Default Routing | | | Not started |
| 14 | OSPF (Single Area) | | | Not started |
| 15 | DHCP & DNS | | | Not started |
| 16 | NAT & PAT | | | Not started |
| 17 | Access Control Lists | | | Not started |
| 18 | IPv6 | | | Not started |
| 19 | Network Security | | | Not started |
| 20 | Automation & Programmability | | | Not started |

---

## Lab Index

| # | Lab | Level | Technologies | Link |
|---|---|---|---|---|
| 2-0 | Hardware Inventory | 1 | `show version`, `show switch`, `show inventory` | |

---

## Cheatsheets

| Topic | Link |
|---|---|
| Subnetting | |
| Show Commands | |
| Troubleshooting Flow | |
| Cisco ↔ MikroTik Command Map | |

---

## Certification Targets

| Certification | Exam Code | Version | Status |
|---|---|---|---|
| CompTIA Network+ | N10-009 | v9 | Studying |
| Cisco CCNA | 200-301 | v2.0 | Studying |
