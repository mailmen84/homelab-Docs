# Switch Port-to-VLAN Map

Switch: **Buffalo BS-MP2012** (core)

> Current cabling reflects [`docs/00-overview/homelab-summary.md`](../00-overview/homelab-summary.md).
> VLAN assignments per port are still to be finalized in pfSense/switch configuration.

## Port map
| Port | Connected device | Mode (planned) | VLAN(s) | Notes |
|---:|---|---|---|---|
| 1 | ISP uplink | access/TBD | TBD | depends on pfSense WAN design |
| 2 | Dell R320 (**not** Proxmox) | access (planned) | TBD | role TBD, target VLAN to confirm |
| 3 | Dell R410 (TrueNAS) | access (planned) | 20 (SERVERS) | TrueNAS SCALE; `192.168.20.11` |
| 4 | PC | access (planned) | 40 (USERS) when VLAN 40 is ready; currently VLAN 20 DHCP | adjust if needed |
| 5 | HPE DL380 (Proxmox, 10Gb) | trunk | 10,20,30,40,50,60 | trunk uplink to virtualization host |
| 6 | Buffalo TS-XL NAS | access (planned) | TBD | likely SERVERS or storage VLAN |

## Other devices (port not yet recorded)
| Device | Likely VLAN | Notes |
|---|---|---|
| Dell R730 (TrueNAS) | 20 (SERVERS) | `192.168.20.12`; switch port TBD |
| PC Workstation (TrueNAS) | 20 (SERVERS) | `192.168.20.13`; switch port TBD |
| Raspberry Pi (Pi-hole) | 10 (INFRA) | `192.168.10.2`; switch port TBD |
| Dell Optiplex (Tailscale) | 10 (INFRA) | `192.168.10.130`; switch port TBD |
| Cisco secondary switch | mgmt | uplink to Buffalo TBD |

## Notes / TODO
- Confirm how WAN is implemented (pfSense placement) and update port 1 accordingly.
- Document the Cisco switch uplink and its port assignments once captured.
- Capture switch ports for R730, PC-TrueNAS, Raspberry Pi, Optiplex.
- Once VLAN 40/50/60 are introduced, fill in access VLANs above and update [`vlan-plan.md`](vlan-plan.md).
