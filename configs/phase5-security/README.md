# Phase 5 — Network Security & Access Control

Harden the network with ACLs, port security, and security best practices.

## Objectives
- [ ] Configure standard ACLs to restrict management access (SSH only from VLAN 10)
- [ ] Configure extended ACLs to isolate GUEST VLAN from internal networks
- [ ] Enable port security on access ports (sticky MAC, violation shutdown)
- [ ] Enable DHCP snooping on access switches
- [ ] Harden VTY lines (access-class, exec-timeout, login local)
- [ ] Encrypt all passwords in running config (`service password-encryption`)
- [ ] Configure enable secret (not enable password)

## Key Commands Introduced
```
! --- Standard ACL for SSH Management ---
access-list 5 permit 10.0.10.0 0.0.0.255
line vty 0 4
  access-class 5 in

! --- Extended ACL: Block Guest from Internal ---
ip access-list extended GUEST_ISOLATION
  deny ip 10.0.40.0 0.0.0.255 10.0.20.0 0.0.0.255
  deny ip 10.0.40.0 0.0.0.255 10.0.50.0 0.0.0.255
  permit ip 10.0.40.0 0.0.0.255 any

interface g0/0.40
  ip access-group GUEST_ISOLATION in

! --- Port Security ---
interface fa0/1
  switchport port-security
  switchport port-security maximum 2
  switchport port-security mac-address sticky
  switchport port-security violation shutdown

! --- DHCP Snooping ---
ip dhcp snooping
ip dhcp snooping vlan 20,30,40
interface g0/1
  ip dhcp snooping trust
```

## Validation Tests
1. SSH from VLAN 10 → router succeeds; SSH from VLAN 20 → router is denied
2. Guest VLAN PC cannot ping corporate or server subnets
3. Guest VLAN PC CAN ping the internet (ISP loopback)
4. Connect unauthorized device → port goes `err-disabled`
5. `show port-security interface fa0/1` — secure MAC addresses visible
6. `show ip dhcp snooping binding` — legitimate bindings present
