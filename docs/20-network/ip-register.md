# IP Address Register

## Active subnets (VLANs in service)
- **VLAN 10 — Management / LAN:** `192.168.10.0/24`
- **VLAN 20 — Servers:** `192.168.20.0/24`
- **VLAN 30 — Management / iLO / Other:** `192.168.30.0/24`

Planned (not yet enforced): VLAN 40 / 50 / 60.

> Historical: an earlier flat `192.168.8.0/24` was used before VLAN segmentation. It is no longer active.
> For the up-to-date per-service IP map (Pi-hole, Proxmox, TrueNAS, iLO, Grafana, etc.) see
> [`network-summary.md`](network-summary.md).

## Static IPs (to confirm)
> Fill values when confirmed. Keep this as the "source of truth" once filled.

| Device | Role | Hostname | IP | VLAN | Notes |
|---|---|---|---|---|---|
| DL380 | Proxmox | TODO | TODO | trunk (multiple) | Switch port 5 |
| R320 | TBD (not Proxmox) | TODO | TODO | access (TBD) | Switch port 2 |
| R410 | TrueNAS | TODO | `192.168.20.112` (per network-summary) | 20 | Switch port 3 |
| Buffalo BS-MP2012 | Switch mgmt | TODO | TODO | 10 (mgmt) | core switch |
| Buffalo TS-XL | NAS | TODO | TODO | access (TBD) | Switch port 6 |
| Raspberry Pi | Pi-hole / monitoring | TODO | `192.168.10.2` (Pi-hole) | 10 | port TBD |
| pfSense | Firewall/router | TODO | `192.168.10.1` (VLAN 10 gw) | trunk | gateway on all VLANs |
| PC (main) | Client | TODO | DHCP/TODO | user (TBD) | Switch port 4 |
| Tailscale subnet router | Ubuntu/NUC | TODO | `192.168.10.130` | 10 | advertises VLAN routes |

## DHCP (to document later)
- DHCP server: pfSense (per VLAN).
- DHCP ranges per VLAN: TODO.
