# Phase 4 — WAN Connectivity, NAT & OSPF

Connect HQ and Branch to the simulated ISP and establish dynamic routing.

## Objectives
- [ ] Configure WAN serial/GigE links between HQ-RTR ↔ ISP-RTR ↔ BR-RTR
- [ ] Configure static default route on HQ-RTR and BR-RTR pointing to ISP
- [ ] Configure PAT (NAT overload) on HQ-RTR for internet access
- [ ] Enable OSPF Area 0 between HQ-RTR and BR-RTR (over GRE tunnel or direct link)
- [ ] Redistribute default route into OSPF
- [ ] Verify Branch can reach HQ subnets and simulated internet

## Key Commands Introduced
```
! --- NAT Configuration (HQ-RTR) ---
access-list 10 permit 10.0.0.0 0.0.255.255
ip nat inside source list 10 interface g0/1 overload

interface g0/0.20
  ip nat inside
interface g0/1
  ip nat outside

! --- OSPF Configuration ---
router ospf 1
  router-id 1.1.1.1
  network 10.0.0.0 0.0.255.255 area 0
  network 172.16.0.0 0.0.0.3 area 0
  default-information originate

! --- Default Route to ISP ---
ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

## Validation Tests
1. `show ip nat translations` — translations visible during pings
2. `show ip ospf neighbor` — adjacency FULL with Branch
3. `show ip route ospf` — Branch subnets learned via OSPF
4. Branch PC can ping HQ server (10.0.50.10)
5. Branch PC can ping simulated internet (ISP loopback 8.8.8.8)
