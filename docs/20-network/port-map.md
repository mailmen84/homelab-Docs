# Switch Port-to-VLAN Map (Draft)

Switch: **Buffalo BS-MP2012**

> Draft map. Update before enforcing VLANs on the switch/pfSense.

## Port map
| Port | Connected device | Mode (planned) | VLAN(s) | Notes |
|---:|---|---|---|---|
| 1 | ISP uplink | access/TBD | TBD | depends on pfSense WAN design |
| 2 | Dell R320 (Proxmox) | access (planned) | TBD | decide target VLAN for hypervisor mgmt |
| 3 | Dell R410 (TrueNAS) | access (planned) | TBD | likely STORAGE or SERVERS depending design |
| 4 | PC | access (planned) | 40 (USERS) | adjust if needed |
| 5 | HPE DL380 (Proxmox, 10Gb) | trunk | 10,20,30,40,50,60 | trunk uplink to virtualization host |
| 6 | Buffalo TS-XL NAS | access (planned) | TBD | likely STORAGE or SERVERS |

## Notes / TODO
- Confirm how WAN is implemented (pfSense placement) and update port 1 accordingly.
- Confirm final access VLAN per server/NAS port.
- Once pfSense is ready, define IP ranges per VLAN and update `ip-register.md`.
