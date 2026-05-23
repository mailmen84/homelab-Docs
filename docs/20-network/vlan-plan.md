# VLAN Plan

> Authoritative source for current VLAN state: [`docs/00-overview/homelab-summary.md`](../00-overview/homelab-summary.md).

Active VLAN IDs: **10 / 20 / 30**
Planned VLAN IDs: **40 / 50 / 60**

## VLANs in service
| VLAN ID | Name | Purpose | Subnet | Gateway | Example devices |
|---:|---|---|---|---|---|
| 10 | INFRA | Infrastructure services | `192.168.10.0/24` | `192.168.10.1` | Pi-hole, Tailscale subnet router |
| 20 | SERVERS | Physical servers, Proxmox VMs, services | `192.168.20.0/24` | `192.168.20.1` | DL380 (Proxmox), R410/R730/PC TrueNAS, all VMs |
| 30 | HW-MGMT | Hardware management (iDRAC/iLO), IoT | `192.168.30.0/24` | `192.168.30.1` | iDRAC on R320/R410/R730, iLO on DL380, IoT devices |

## Planned VLANs
| VLAN ID | Name (draft) | Purpose (draft) | Example devices |
|---:|---|---|---|
| 40 | USERS | PCs, laptops, phones | client devices |
| 50 | IOT | Smart-home / IoT segment (if split from VLAN 30) | cameras, smart devices |
| 60 | GUEST | Guest Wi-Fi / devices | guest clients |

> Final names/purposes for 40/50/60 still need confirmation before enforcement.

## Notes
- Trunk planned on switch port 5 (DL380 uplink); carries all six VLANs.
- Access VLAN per other port is documented in [`port-map.md`](port-map.md).
- IP ranges per VLAN 10/20/30 are already in effect; 40/50/60 ranges will be defined when those VLANs are introduced.
