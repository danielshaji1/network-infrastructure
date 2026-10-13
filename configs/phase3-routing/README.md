# Phase 3 — Inter-VLAN Routing & DHCP

Enable communication between VLANs using router-on-a-stick and provide dynamic addressing.

## Objectives
- [ ] Configure sub-interfaces on HQ-RTR for each VLAN
- [ ] Enable DHCP pools on HQ-RTR or DHCP server for VLANs 20, 30, 40
- [ ] Configure DHCP relay (ip helper-address) if using a centralized DHCP server
- [ ] Verify inter-VLAN routing (PCs across VLANs can ping each other)
- [ ] Verify DHCP leases are assigned correctly

## Key Commands Introduced
```
interface g0/0
  no shutdown

interface g0/0.20
  encapsulation dot1Q 20
  ip address 10.0.20.1 255.255.255.0

ip dhcp excluded-address 10.0.20.1 10.0.20.10
ip dhcp pool CORP_POOL
  network 10.0.20.0 255.255.255.0
  default-router 10.0.20.1
  dns-server 10.0.50.10

interface g0/0.20
  ip helper-address 10.0.50.30
```

## Validation Tests
1. PCs receive DHCP addresses: `ipconfig /renew`
2. `show ip dhcp binding` on router — active leases visible
3. PC in VLAN 20 can ping PC in VLAN 30 (inter-VLAN)
4. PC in VLAN 20 can ping server in VLAN 50
5. `traceroute` shows the router as the hop between VLANs
