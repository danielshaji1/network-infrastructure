# Enterprise Network Infrastructure — Cisco Packet Tracer Lab

A progressive, multi-phase enterprise network built in **Cisco Packet Tracer**, designed as a hands-on learning lab from beginner fundamentals through intermediate concepts, with stretch goals reaching into advanced territory.

> **Related Project**: [network-ops-automation](https://github.com/danielshaji1/network-ops-automation) — An AI Agent framework using Google Antigravity SDK that generates Ansible playbooks from network intent and stages them through a Human-in-the-Loop GitOps pipeline.

---

## Architecture at a Glance

```
                              ┌──────────────┐
                              │   ISP Cloud   │
                              │  (Simulated)  │
                              └──┬────────┬───┘
                  203.0.113.0/30 │        │ 198.51.100.0/30
                    ┌────────────┘        └────────────┐
              ┌─────┴─────┐                     ┌──────┴─────┐
              │  HQ-RTR   │  ···GRE Tunnel···   │   BR-RTR   │
              │  (2911)   │  172.16.0.0/30      │   (2911)   │
              └─────┬─────┘                     └──────┬─────┘
                    │ Router-on-a-Stick                 │
                    │ VLANs 10,20,30,40,50             │ VLANs 10,20
           ┌────────┴────────┐                   ┌─────┴──────┐
     ┌─────┴─────┐     ┌────┴──────┐       ┌────┴─────┐
     │  HQ-DSW1  │─EC──│  HQ-DSW2  │       │  BR-ASW1 │
     │  (2960)   │     │  (2960)   │       │  (2960)  │
     └─────┬─────┘     └─────┬─────┘       └────┬─────┘
           │                 │                   │
     ┌─────┴─────┐     ┌────┴──────┐       Branch PCs
     │  HQ-ASW1  │     │  HQ-ASW2  │       (VLAN 20)
     │  (2960)   │     │  (2960)   │
     └─────┬─────┘     └─────┬─────┘
           │                 │
    ┌──────┼────┐     ┌──────┼──────┐
    │ PCs  │ IP │     │Servers│ APs │
    │VLN20 │Phn │     │VLN 50 │VLN40│
    │      │V30 │     │       │     │
    └──────┴────┘     └───────┴─────┘
```

**EC** = EtherChannel (LACP) · **VLN** = VLAN

---

## Build Phases (Learning Progression)

The project follows a phased approach — each phase builds on the previous and introduces new concepts:

| Phase | Focus | Key Skills | Status |
|-------|-------|-----------|--------|
| [**Phase 1**](configs/phase1-base/) | Base Infrastructure | Hostnames, SSH, IP addressing, cabling | ⬜ Not Started |
| [**Phase 2**](configs/phase2-vlans/) | VLANs & Trunking | 802.1Q, native VLAN hardening, EtherChannel, port security basics | ⬜ Not Started |
| [**Phase 3**](configs/phase3-routing/) | Inter-VLAN Routing & DHCP | Router-on-a-stick, sub-interfaces, DHCP pools/relay | ⬜ Not Started |
| [**Phase 4**](configs/phase4-wan/) | WAN, NAT & OSPF | ISP uplinks, PAT overload, OSPF Area 0, default route redistribution | ⬜ Not Started |
| [**Phase 5**](configs/phase5-security/) | Security & ACLs | Standard/extended ACLs, port security, DHCP snooping, management hardening | ⬜ Not Started |

---

## Stretch Goals

After completing all 5 phases, push further with [**13 stretch goals**](STRETCH_GOALS.md) organized into three difficulty tiers:

| Tier | Goals | Examples |
|------|-------|---------|
| 🔵 **Intermediate** | S1 – S4 | GRE Tunnels, HSRP Redundancy, Rapid PVST+, IPv6 Dual-Stack |
| 🟡 **Advanced** | S5 – S9 | IPsec VPN, QoS, SNMP/Syslog, Multi-Area OSPF, eBGP |
| 🔴 **Portfolio Showcase** | S10 – S13 | Ansible Automation, Docs Site, WLC/Wireless, DMZ + Zone-Based Firewall |

---

## Repository Structure

```
network-infrastructure/
├── configs/
│   ├── phase1-base/           # Phase 1: Device setup, SSH, IP addressing
│   ├── phase2-vlans/          # Phase 2: VLANs, trunks, EtherChannel
│   ├── phase3-routing/        # Phase 3: Inter-VLAN routing, DHCP
│   ├── phase4-wan/            # Phase 4: WAN, NAT/PAT, OSPF
│   ├── phase5-security/       # Phase 5: ACLs, port security, hardening
│   └── templates/             # Reusable IOS config templates
│       ├── router-base.txt
│       ├── switch-base.txt
│       ├── trunk-config.txt
│       ├── etherchannel-lacp.txt
│       ├── roas-subinterface.txt
│       ├── ospf-config.txt
│       └── nat-pat.txt
├── docs/
│   ├── ip-addressing.md       # Full IPAM: VLANs, subnets, DHCP pools
│   ├── topology-diagram.md    # ASCII topology + device inventory
│   └── design-decisions.md    # Rationale behind architecture choices
├── STRETCH_GOALS.md           # 13 stretch goals with progress tracker
└── README.md
```

---

## VLAN Summary

| VLAN | Name | Subnet | Purpose |
|------|------|--------|---------|
| 10 | MGMT | 10.0.10.0/24 | Network device management |
| 20 | CORP_DATA | 10.0.20.0/24 | Corporate workstations |
| 30 | VOIP | 10.0.30.0/24 | IP telephony |
| 40 | GUEST_WIFI | 10.0.40.0/24 | Isolated guest access |
| 50 | SERVERS | 10.0.50.0/24 | Server farm (DNS, Web, DHCP) |
| 99 | NATIVE_TRUNK | — | Hardened trunk native VLAN |
| 100 | BLACKHOLE | — | Unused ports (security) |

---

## Getting Started

### Prerequisites
- [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer) (free with Networking Academy account)
- Basic familiarity with CLI navigation (`enable`, `configure terminal`, `show` commands)

### Workflow
1. **Read the phase README** — understand objectives and key commands
2. **Open Packet Tracer** — place devices per the [topology diagram](docs/topology-diagram.md)
3. **Apply configs** — use the [templates](configs/templates/) as starting points, customize per the [IP addressing plan](docs/ip-addressing.md)
4. **Validate** — run every test listed in the phase README before moving on
5. **Commit your progress** — save your `.pkt` file and update phase status in this README

### Tips
- Use `show running-config` liberally to verify what's actually configured
- Always `write memory` (or `copy running-config startup-config`) after changes
- If something breaks, `show log` and `debug` are your friends
- Refer to [design decisions](docs/design-decisions.md) to understand _why_, not just _how_

---

## Tech Stack & Concepts Covered

**Protocols & Technologies**: Ethernet, 802.1Q, STP/RSTP, LACP, OSPF, NAT/PAT, DHCP, DNS, SSH, ACLs, GRE, IPsec, HSRP, BGP, QoS, SNMP, Syslog, IPv6

**Cisco IOS Features**: Router-on-a-Stick, Sub-interfaces, EtherChannel, Port Security, DHCP Snooping, BPDU Guard, Zone-Based Firewall

**Certifications Aligned**: CCNA 200-301, CCNP ENCOR 350-401 (stretch goals)
