# Stretch Goals & Advanced Milestones

Beyond the core 5-phase build, these stretch goals push the network design toward production-grade patterns. Each goal is independent — tackle them in any order after completing the base phases.

---

## 🔵 Tier 1 — Intermediate Extensions

### S1: Site-to-Site GRE Tunnel
- **What**: Build a GRE tunnel between HQ-RTR and BR-RTR over the simulated ISP, encapsulating OSPF traffic.
- **Why**: GRE is the foundation for multi-site overlays and is frequently tested on CCNA/CCNP.
- **Key Config**:
  ```
  interface tunnel 0
    ip address 172.16.0.1 255.255.255.252
    tunnel source g0/1
    tunnel destination 198.51.100.2
  ```
- **Validation**: `show interface tunnel 0`, OSPF neighbor forms over tunnel, Branch PC reaches HQ servers.

---

### S2: HSRP First-Hop Redundancy
- **What**: Deploy HSRP between HQ-DSW1 and HQ-DSW2 (requires upgrading to L3 3560 switches) so that the default gateway for each VLAN is a virtual IP.
- **Why**: Eliminates single points of failure at the gateway. Widely deployed in enterprise campus networks.
- **Key Config**:
  ```
  interface vlan 20
    standby 20 ip 10.0.20.1
    standby 20 priority 110
    standby 20 preempt
  ```
- **Validation**: `show standby brief`, unplug active switch → failover within seconds, PCs maintain connectivity.

---

### S3: Spanning Tree Tuning
- **What**: Configure Rapid PVST+ with root bridge election per VLAN, PortFast on access ports, and BPDU Guard.
- **Why**: Prevents broadcast storms and ensures deterministic forwarding paths.
- **Key Config**:
  ```
  spanning-tree mode rapid-pvst
  spanning-tree vlan 20 root primary    ! on HQ-DSW1
  spanning-tree vlan 30 root secondary  ! on HQ-DSW1

  interface range fa0/1-10
    spanning-tree portfast
    spanning-tree bpduguard enable
  ```
- **Validation**: `show spanning-tree summary`, `show spanning-tree vlan 20` → correct root bridge, connect a rogue switch → port goes err-disabled.

---

### S4: IPv6 Dual-Stack
- **What**: Assign IPv6 addresses (ULA fd00::/7 or documentation 2001:db8::/32) alongside existing IPv4 on all VLANs. Enable OSPFv3 or EIGRPv6.
- **Why**: IPv6 is a CCNA exam objective and increasingly deployed in production.
- **Key Config**:
  ```
  ipv6 unicast-routing
  interface g0/0.20
    ipv6 address 2001:db8:20::1/64
    ipv6 address fe80::1 link-local
  ```
- **Validation**: `ping ipv6` between VLANs, `show ipv6 route`, `show ipv6 ospf neighbor`.

---

## 🟡 Tier 2 — Advanced Challenges

### S5: Site-to-Site IPsec VPN
- **What**: Encrypt the GRE tunnel between HQ and Branch using IPsec (IKEv1 or IKEv2, depending on PT support).
- **Why**: Secures site-to-site traffic over untrusted ISP links. Core skill for network security roles.
- **Key Concepts**: ISAKMP policy, transform sets, crypto map or tunnel protection.
- **Validation**: `show crypto isakmp sa` → QM_IDLE, `show crypto ipsec sa` → packet counters incrementing.

---

### S6: QoS — Prioritizing Voice Traffic
- **What**: Mark VOIP VLAN traffic with DSCP EF, configure priority queuing on uplinks, and police bandwidth for guest traffic.
- **Why**: Demonstrates understanding of traffic engineering and SLA enforcement.
- **Key Config**:
  ```
  class-map match-all VOIP_TRAFFIC
    match ip dscp ef
  policy-map QOS_POLICY
    class VOIP_TRAFFIC
      priority percent 30
    class class-default
      fair-queue
  interface g0/0
    service-policy output QOS_POLICY
  ```
- **Validation**: `show policy-map interface`, traffic simulation showing VOIP prioritized over bulk data.

---

### S7: SNMP & Syslog Monitoring
- **What**: Configure SNMP v2c/v3 on all devices pointing to a management server. Send syslog messages to a central server in VLAN 50.
- **Why**: Operational visibility — you can't manage what you can't measure.
- **Key Config**:
  ```
  snmp-server community {{COMMUNITY_STRING}} RO 5
  snmp-server host 10.0.50.40 version 2c {{COMMUNITY_STRING}}
  logging host 10.0.50.40
  logging trap informational
  ```
- **Validation**: Syslog server receives messages when interfaces toggle, SNMP walk returns device info.

---

### S8: Multi-Area OSPF
- **What**: Redesign OSPF so HQ is Area 0 (backbone), Branch is Area 1, and configure inter-area summarization on the ABR.
- **Why**: Teaches hierarchical OSPF design, LSA types, and route summarization — critical CCNP-level knowledge.
- **Key Config**:
  ```
  ! On HQ-RTR (ABR)
  router ospf 1
    network 10.0.0.0 0.0.255.255 area 0
    network 172.16.0.0 0.0.0.3 area 1
    area 1 range 10.1.0.0 255.255.0.0
  ```
- **Validation**: `show ip ospf database` — see Type 3 LSAs, `show ip route ospf` — summarized routes visible.

---

### S9: BGP Peering with ISP
- **What**: Replace the static default route with eBGP peering between HQ-RTR (AS 65001) and ISP-RTR (AS 65000). Advertise the public prefix.
- **Why**: BGP is the protocol of the internet. Understanding eBGP basics is a massive differentiator.
- **Key Config**:
  ```
  router bgp 65001
    neighbor 203.0.113.1 remote-as 65000
    network 203.0.113.0 mask 255.255.255.0
  ```
- **Validation**: `show ip bgp summary` → Established state, `show ip bgp` → routes learned from ISP.

---

## 🔴 Tier 3 — Portfolio Showcase Goals

### S10: Network Automation with Ansible (Outside Packet Tracer)
- **What**: Export device configurations and write Ansible playbooks to programmatically deploy them. Link to the [network-ops-automation](https://github.com/danielshaji1/network-ops-automation) repo.
- **Why**: Demonstrates the bridge between traditional CLI networking and modern NetDevOps practices.

---

### S11: Full Documentation Site
- **What**: Build a GitHub Pages site (or MkDocs) from this repo's Markdown docs, with searchable topology diagrams and per-phase walkthroughs.
- **Why**: Shows communication skills and attention to documentation — highly valued by hiring managers.

---

### S12: Wireless LAN Controller (WLC) + Lightweight APs
- **What**: Add a WLC and lightweight access points to HQ. Configure SSIDs mapped to VLANs 20 (Corporate WPA2-Enterprise) and 40 (Guest captive portal).
- **Why**: Wireless is ubiquitous in enterprise networks. PT supports basic WLC simulation.

---

### S13: DMZ with Zone-Based Firewall
- **What**: Add a DMZ VLAN (VLAN 60) hosting a public-facing web server. Configure zone-based policy firewall (ZBF) on HQ-RTR to enforce traffic flow: INSIDE → DMZ (allowed), OUTSIDE → DMZ (HTTP/HTTPS only), DMZ → INSIDE (denied).
- **Why**: Core security architecture pattern. Demonstrates defense-in-depth.

---

## Progress Tracker

| Goal | Status | Phase Dependency | Difficulty   |
|------|--------|------------------|--------------|
| S1   | ⬜ Not Started | Phase 4    | ⭐⭐         |
| S2   | ⬜ Not Started | Phase 2    | ⭐⭐         |
| S3   | ⬜ Not Started | Phase 2    | ⭐⭐         |
| S4   | ⬜ Not Started | Phase 3    | ⭐⭐⭐       |
| S5   | ⬜ Not Started | S1         | ⭐⭐⭐       |
| S6   | ⬜ Not Started | Phase 3    | ⭐⭐⭐       |
| S7   | ⬜ Not Started | Phase 3    | ⭐⭐         |
| S8   | ⬜ Not Started | Phase 4    | ⭐⭐⭐       |
| S9   | ⬜ Not Started | Phase 4    | ⭐⭐⭐⭐     |
| S10  | ⬜ Not Started | Phase 5    | ⭐⭐⭐       |
| S11  | ⬜ Not Started | Any        | ⭐⭐         |
| S12  | ⬜ Not Started | Phase 3    | ⭐⭐⭐       |
| S13  | ⬜ Not Started | Phase 5    | ⭐⭐⭐⭐     |
