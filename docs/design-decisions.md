# Design Decisions & Rationale

This document captures the reasoning behind key architectural choices in the network design.

---

## 1. Router-on-a-Stick for Inter-VLAN Routing

**Decision**: Use sub-interfaces on HQ-RTR (G0/0.X) instead of Layer 3 switching.

**Rationale**: Cisco Packet Tracer's 2960 switches do not support Layer 3 SVIs for routing. Router-on-a-stick is the standard approach in PT labs and maps directly to CCNA exam objectives. This can be upgraded to a multilayer switch (3560) as a stretch goal.

---

## 2. Dedicated VLAN for Trunk Native Traffic (VLAN 99)

**Decision**: Change the native VLAN on all trunk links from VLAN 1 to VLAN 99.

**Rationale**: VLAN 1 is the default and cannot be deleted. Changing the native VLAN mitigates VLAN hopping attacks (double tagging). This is a security best practice reinforced in CyberOps and CCNA Security curricula.

---

## 3. BLACKHOLE VLAN (VLAN 100) for Unused Ports

**Decision**: All unused switch ports are assigned to VLAN 100 and administratively shut down.

**Rationale**: Prevents unauthorized devices from accessing the network. This is a CIS Benchmark and NIST 800-53 recommended hardening control.

---

## 4. Separate Management VLAN (VLAN 10)

**Decision**: Isolate switch management traffic into its own VLAN.

**Rationale**: Keeps management plane traffic (SSH, SNMP, Syslog) off the user data plane. Limits blast radius if a user VLAN is compromised.

---

## 5. RFC 5737 / RFC 1918 Addressing

**Decision**: Use 10.0.0.0/8 for internal subnets and 203.0.113.0/24 + 198.51.100.0/24 for WAN/ISP simulation.

**Rationale**: RFC 1918 private ranges for internal use; RFC 5737 documentation ranges for ISP-facing links ensure the lab never conflicts with real internet addresses.

---

## 6. OSPF as Interior Gateway Protocol

**Decision**: Use OSPF (single area initially, multi-area as stretch goal) between HQ-RTR and BR-RTR.

**Rationale**: OSPF is the most widely tested IGP on the CCNA. It provides fast convergence, supports VLSM, and scales well. Starting with single-area OSPF (Area 0) keeps the initial build simple.

---

## 7. EtherChannel Between Distribution Switches

**Decision**: Configure LACP EtherChannel between HQ-DSW1 and HQ-DSW2.

**Rationale**: Demonstrates link aggregation for bandwidth and redundancy. LACP (802.3ad) is the open standard and preferred over PAgP for vendor neutrality.
