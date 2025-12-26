# Network Summary (Current + Planned)

## Current state (temporary)
- Main subnet (temporary): **192.168.8.0/24**
- Notes:
  - This is a transitional setup before the full VLAN segmentation is enforced.
  - All devices currently communicate on the same LAN (flat network).

## Planned segmentation (VLAN plan)
Planned VLAN IDs:
- **10**
- **20**
- **30**
- **40**
- **50**
- **60**

> VLAN names/purposes will be defined in a dedicated page later (e.g. `vlan-plan.md`).
> For now we record the IDs and trunk/access intent.

## Switch: Buffalo BS-MP2012 (port map)
Current cabling / port usage:
- Port 1: ISP (internet uplink)
- Port 2: R320 (Proxmox)
- Port 3: R410 (TrueNAS)
- Port 4: PC
- Port 5: DL380 (Proxmox, **10Gb**, planned **trunk**)
- Port 6: Buffalo TS-XL NAS

## Trunk vs access (planned)
- Port 5: **Trunk** (VLANs 10/20/30/40/50/60) to DL380
- Other ports: **Access** ports (as previously defined)
  - TODO: write the explicit “port → VLAN” access table once confirmed

## Open questions / TODO
- Confirm which device will be the VLAN gateway/router (pfSense planned).
- Confirm final IP ranges per VLAN (after pfSense design).
- Confirm which ports are management vs user vs storage.
