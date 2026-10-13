# Network Topology Diagram

High-level logical view of the enterprise network built in Cisco Packet Tracer.

---

## Logical Topology (HQ + Branch + WAN)

```
                              ┌──────────────┐
                              │   ISP Cloud   │
                              │  (Simulated)  │
                              └──┬────────┬───┘
                  203.0.113.0/30 │        │ 198.51.100.0/30
                                 │        │
                    ┌────────────┘        └────────────┐
                    │                                  │
              ┌─────┴─────┐                     ┌──────┴─────┐
              │  HQ-RTR   │  ···GRE Tunnel···   │   BR-RTR   │
              │  (Router) │  172.16.0.0/30      │  (Router)  │
              └─────┬─────┘                     └──────┬─────┘
                    │ G0/0 (Router-on-a-Stick)         │ G0/0
                    │ VLANs 10,20,30,40,50             │ VLANs 10,20
                    │                                  │
           ┌────────┴────────┐                   ┌─────┴──────┐
           │                 │                   │            │
     ┌─────┴─────┐     ┌─────┴─────┐       ┌────┴─────┐      │
     │  HQ-DSW1  │─────│  HQ-DSW2  │       │  BR-ASW1 │      │
     │  (L2 Dist)│ EC  │  (L2 Dist)│       │ (Access) │      │
     └─────┬─────┘     └─────┬─────┘       └────┬─────┘      │
           │                 │                   │            │
     ┌─────┴─────┐     ┌─────┴─────┐       ┌────┴─────┐      │
     │  HQ-ASW1  │     │  HQ-ASW2  │       │ Branch   │      │
     │ (Access)  │     │ (Access)  │       │   PCs    │      │
     └─────┬─────┘     └─────┬─────┘       └──────────┘      │
           │                 │                                │
   ┌───────┼────────┐  ┌─────┼────────┐                       │
   │  PCs  │Phones  │  │Servers│ APs  │                       │
   │VLAN20 │VLAN30  │  │VLAN50 │VLAN40│                       │
   └───────┴────────┘  └──────┴──────┘                        │
                                                              │

   EC = EtherChannel (Link Aggregation)
```

---

## Physical Device Inventory

| Device     | Model (Packet Tracer)   | Role                         | Location |
|------------|-------------------------|------------------------------|----------|
| HQ-RTR     | 2911                    | Core router, inter-VLAN, WAN | HQ       |
| HQ-DSW1    | 2960-24TT               | Distribution switch          | HQ       |
| HQ-DSW2    | 2960-24TT               | Distribution switch          | HQ       |
| HQ-ASW1    | 2960-24TT               | Access switch (floor 1)      | HQ       |
| HQ-ASW2    | 2960-24TT               | Access switch (floor 2)      | HQ       |
| BR-RTR     | 2911                    | Branch router, WAN           | Branch   |
| BR-ASW1    | 2960-24TT               | Branch access switch         | Branch   |
| ISP-RTR    | 2911                    | Simulated ISP router         | WAN      |
| SRV-DNS    | Server-PT               | DNS services                 | HQ DC    |
| SRV-WEB    | Server-PT               | Web server (HTTP/HTTPS)      | HQ DC    |
| SRV-DHCP   | Server-PT               | DHCP server                  | HQ DC    |
