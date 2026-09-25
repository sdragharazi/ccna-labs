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
| 00 | Lab Tools (Packet Tracer & PNETLab) | | | Not started |
| 01 | Network Fundamentals | [notes](notes/01-network-fundamentals.md) | | Done |
| 02 | Network Devices | [notes](notes/02-network-devices.md) | | In progress |
| 03 | OSI Model & Encapsulation | | | Not started |
| 04 | Ethernet & Switching | | | Not started |
| 05 | ARP & MAC Address Table | | | Not started |
| 06 | IPv4 Addressing | | | Not started |
| 07 | Subnetting & VLSM | | | Not started |
| 08 | Cisco IOS Fundamentals | | | Not started |
| 09 | VLAN & Trunking | | | Not started |
| 10 | Inter-VLAN Routing | | | Not started |
| 11 | STP & RSTP | | | Not started |
| 12 | EtherChannel | | |
