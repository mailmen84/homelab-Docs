# VLAN Plan (Draft)

Planned VLAN IDs: **10 / 20 / 30 / 40 / 50 / 60**

> This is a draft. Names/purposes can be adjusted before enforcing VLANs in pfSense/switch.

## VLANs
| VLAN ID | Name (draft) | Purpose | Example devices |
|---:|---|---|---|
| 10 | MGMT | Management network for infrastructure | switch mgmt, hypervisors mgmt, iLO/iDRAC |
| 20 | SERVERS | Server services network | Proxmox VMs/CTs, internal services |
| 30 | STORAGE | Storage traffic (if separated) | TrueNAS, storage VLAN |
| 40 | USERS | PCs, laptops, phones | client devices |
| 50 | IOT | cameras, smart devices | cameras, IoT |
| 60 | GUEST | guest Wi-Fi/devices | guest clients |

## Notes
- Trunk planned on switch port 5 (DL380 uplink).
- Access VLAN per port will be documented after the final mapping is confirmed.
- IP ranges per VLAN will be defined during pfSense design.
