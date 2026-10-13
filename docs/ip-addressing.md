# IP Addressing & VLAN Scheme

All addresses use RFC 5737 / RFC 1918 documentation and private ranges.

---

## VLAN Database

| VLAN ID | Name            | Purpose                          | Subnet              | Default Gateway  |
|---------|-----------------|----------------------------------|----------------------|------------------|
| 1       | default         | Native VLAN (unused for traffic) | —                    | —                |
| 10      | MGMT            | Network management               | 10.0.10.0/24         | 10.0.10.1        |
| 20      | CORP_DATA       | Corporate workstations           | 10.0.20.0/24         | 10.0.20.1        |
| 30      | VOIP            | Voice over IP phones             | 10.0.30.0/24         | 10.0.30.1        |
| 40      | GUEST_WIFI      | Isolated guest wireless          | 10.0.40.0/24         | 10.0.40.1        |
| 50      | SERVERS         | Server farm / services           | 10.0.50.0/24         | 10.0.50.1        |
| 99      | NATIVE_TRUNK    | Trunk native VLAN (hardened)     | —                    | —                |
| 100     | BLACKHOLE       | Unused ports (security)          | —                    | —                |

---

## Site Addressing Plan

### Headquarters (HQ) — 10.0.0.0/16

| Device / Role              | Interface      | IP Address       | Subnet Mask       | Notes                    |
|----------------------------|----------------|------------------|--------------------|--------------------------|
| HQ-RTR (Core Router)       | G0/0           | 10.0.10.1        | 255.255.255.0      | MGMT gateway (SVI)       |
| HQ-RTR                     | G0/0.20        | 10.0.20.1        | 255.255.255.0      | CORP_DATA sub-interface   |
| HQ-RTR                     | G0/0.30        | 10.0.30.1        | 255.255.255.0      | VOIP sub-interface        |
| HQ-RTR                     | G0/0.40        | 10.0.40.1        | 255.255.255.0      | GUEST sub-interface       |
| HQ-RTR                     | G0/0.50        | 10.0.50.1        | 255.255.255.0      | SERVERS sub-interface     |
| HQ-RTR                     | G0/1           | 203.0.113.2      | 255.255.255.252    | WAN uplink to ISP         |
| HQ-DSW1 (Distribution SW)  | VLAN 10 SVI    | 10.0.10.2        | 255.255.255.0      | Management IP             |
| HQ-DSW2 (Distribution SW)  | VLAN 10 SVI    | 10.0.10.3        | 255.255.255.0      | Management IP             |
| HQ-ASW1 (Access Switch)    | VLAN 10 SVI    | 10.0.10.11       | 255.255.255.0      | Management IP             |
| HQ-ASW2 (Access Switch)    | VLAN 10 SVI    | 10.0.10.12       | 255.255.255.0      | Management IP             |
| DNS Server                 | Fa0            | 10.0.50.10       | 255.255.255.0      | Internal DNS              |
| Web Server                 | Fa0            | 10.0.50.20       | 255.255.255.0      | Internal HTTP/HTTPS       |
| DHCP Server                | Fa0            | 10.0.50.30       | 255.255.255.0      | DHCP for VLANs 20,30,40  |

### Branch Office — 10.1.0.0/16

| Device / Role              | Interface      | IP Address       | Subnet Mask       | Notes                    |
|----------------------------|----------------|------------------|--------------------|--------------------------|
| BR-RTR (Branch Router)     | G0/0           | 10.1.10.1        | 255.255.255.0      | MGMT gateway             |
| BR-RTR                     | G0/0.20        | 10.1.20.1        | 255.255.255.0      | CORP_DATA sub-interface   |
| BR-RTR                     | G0/1           | 198.51.100.2     | 255.255.255.252    | WAN uplink to ISP         |
| BR-ASW1 (Access Switch)    | VLAN 10 SVI    | 10.1.10.11       | 255.255.255.0      | Management IP             |

### WAN / ISP Links

| Link                       | Network            | Device A IP      | Device B IP      |
|----------------------------|--------------------|------------------|------------------|
| HQ ↔ ISP                  | 203.0.113.0/30     | 203.0.113.2 (HQ) | 203.0.113.1 (ISP)|
| Branch ↔ ISP              | 198.51.100.0/30    | 198.51.100.2 (BR)| 198.51.100.1 (ISP)|
| HQ ↔ Branch (tunnel)      | 172.16.0.0/30      | 172.16.0.1 (HQ)  | 172.16.0.2 (BR)  |

---

## DHCP Pools

| Pool Name     | Network        | Default Gateway | DNS Server   | Excluded Range         |
|---------------|----------------|-----------------|--------------|------------------------|
| CORP_POOL     | 10.0.20.0/24   | 10.0.20.1       | 10.0.50.10   | 10.0.20.1 – 10.0.20.10 |
| VOIP_POOL     | 10.0.30.0/24   | 10.0.30.1       | 10.0.50.10   | 10.0.30.1 – 10.0.30.10 |
| GUEST_POOL    | 10.0.40.0/24   | 10.0.40.1       | 10.0.50.10   | 10.0.40.1 – 10.0.40.10 |
| BR_CORP_POOL  | 10.1.20.0/24   | 10.1.20.1       | 10.0.50.10   | 10.1.20.1 – 10.1.20.10 |
