# IP Address Register

## Current subnet (temporary)
- Network: **192.168.8.0/24**
- Notes: temporary flat network before VLAN rollout.

## Static IPs (to confirm)
> Fill values when confirmed. Keep this as the “source of truth”.

| Device | Role | Hostname | IP | VLAN (future) | Notes |
|---|---|---|---|---|---|
| DL380 | Proxmox | TODO | TODO | trunk (multiple) | Switch port 5 |
| R320 | Proxmox | TODO | TODO | access (TBD) | Switch port 2 |
| R410 | TrueNAS | TODO | TODO | access (TBD) | Switch port 3 |
| Buffalo BS-MP2012 | Switch mgmt | TODO | TODO | mgmt (TBD) | core switch |
| Buffalo TS-XL | NAS | TODO | TODO | access (TBD) | Switch port 6 |
| Raspberry Pi | Monitoring | TODO | TODO | services (TBD) | port TBD |
| PC (main) | Client | TODO | DHCP/TODO | user (TBD) | Switch port 4 |

## DHCP (to document later)
- DHCP server: TODO (likely pfSense later)
- DHCP ranges per VLAN: TODO
