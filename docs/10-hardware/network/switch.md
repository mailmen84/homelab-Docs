# Switch — Buffalo BS-MP2012

## Purpose
Core connectivity for the homelab. Provides port-based connections for servers, NAS, and PCs.
Planned: VLAN segmentation with a trunk uplink to DL380 (Proxmox).

## Current port map (physical cabling)
- Port 1: ISP / internet uplink
- Port 2: Dell R320 (Proxmox)
- Port 3: Dell R410 (TrueNAS)
- Port 4: PC (management/client)
- Port 5: HPE DL380 Gen9 (Proxmox) — 10Gb uplink, planned trunk
- Port 6: Buffalo TS-XL NAS

## VLAN plan (target)
Planned VLAN IDs: 10 / 20 / 30 / 40 / 50 / 60

Trunk plan:
- Port 5: trunk (carry VLANs 10/20/30/40/50/60)

Access ports:
- Ports 1/2/3/4/6: access ports (exact VLAN per port to be finalized and documented)

## Notes / TODO
- Confirm switch management IP and how the switch is accessed (web UI / CLI).
- Document the final access VLAN assignment for each port.
- Document any LACP/port-channel usage (if used later).
