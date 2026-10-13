# Phase 2 — VLAN Segmentation & Trunking

Break the flat LAN into purpose-built VLANs with trunk links between switches.

## Objectives
- [ ] Create VLANs 10, 20, 30, 40, 50, 99, 100 on all switches
- [ ] Assign access ports to correct VLANs
- [ ] Configure trunk links (802.1Q) between switches
- [ ] Set native VLAN to 99 on all trunks
- [ ] Shut down unused ports and assign to VLAN 100 (BLACKHOLE)
- [ ] Configure EtherChannel (LACP) between HQ-DSW1 and HQ-DSW2
- [ ] Verify VLAN database and trunk status

## Key Commands Introduced
```
vlan 20
  name CORP_DATA

interface range fa0/1-10
  switchport mode access
  switchport access vlan 20

interface range g0/1-2
  switchport mode trunk
  switchport trunk native vlan 99
  switchport trunk allowed vlan 10,20,30,40,50,99

interface range fa0/23-24
  channel-group 1 mode active
  switchport mode trunk

interface range fa0/20-22
  switchport mode access
  switchport access vlan 100
  shutdown
```

## Validation Tests
1. `show vlan brief` — VLANs present with correct port assignments
2. `show interfaces trunk` — trunks carrying expected VLANs
3. `show etherchannel summary` — port-channel is SU (Layer2, in use)
4. PCs in the same VLAN can ping each other
5. PCs in different VLANs CANNOT ping each other (no routing yet)
