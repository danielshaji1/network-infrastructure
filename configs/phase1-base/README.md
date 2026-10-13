# Phase 1 — Base Infrastructure

Flat LAN with basic connectivity. No VLANs, no routing — just get devices talking.

## Objectives
- [ ] Place all devices on the Packet Tracer canvas
- [ ] Cable connections per topology diagram
- [ ] Assign hostnames and banners to all network devices
- [ ] Configure console and VTY passwords
- [ ] Enable SSH (disable Telnet)
- [ ] Assign IP addresses to all end devices and server NICs
- [ ] Verify end-to-end connectivity with `ping`

## Key Commands Introduced
```
hostname HQ-RTR
banner motd #Unauthorized access prohibited#
line console 0
  password <console_password>
  login
line vty 0 4
  transport input ssh
  password <vty_password>
  login local
username admin privilege 15 secret <admin_password>
ip domain-name lab.local
crypto key generate rsa modulus 2048
ip ssh version 2
```

## Validation Tests
1. `ping` from any PC to the default gateway
2. SSH into each switch and router from a management PC
3. `show ip interface brief` — all assigned interfaces should be `up/up`
