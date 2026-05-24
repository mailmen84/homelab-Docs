# HPE ProLiant DL380 Gen9 — Proxmox

## Purpose
Main compute node running Proxmox. Planned trunk uplink to the switch for VLANs.

## Physical / connectivity
- Connected switch port: **Port 5**
- Link: **10Gb** (as currently planned/used)
- Switch notes: Port 5 planned as **trunk** carrying VLANs 10/20/30/40/50/60

## Network
- Active VLANs in the homelab: **10 / 20 / 30** (`192.168.10.0/24`, `192.168.20.0/24`, `192.168.30.0/24`).
- Proxmox node IP: `192.168.20.10` (VLAN 20).
- MAC: `9c:dc:71:6e:14:28`.
- Hostname: `proxmox`.
- iLO management IP: TODO (expected on VLAN 30).

## Proxmox notes
- Version: **Proxmox VE 9.2.2**.
- Cluster: TODO (currently appears standalone).
- Bridges: TODO (e.g., vmbr0, VLAN-aware yes/no).
- Storage used by Proxmox: TODO (local disks / ZFS / shared storage on R410/R730).
- VMs hosted here: see [`docs/40-virtualization/vm-inventory.md`](../../40-virtualization/vm-inventory.md).

## Hardware summary (fill later)
- CPU(s): TODO
- RAM: TODO
- Disks/RAID/HBA: TODO
- NICs: TODO (model + ports used)

## TODO (data to collect)
- Proxmox version
- IP address(es)
- Bridge/VLAN configuration
- Disk layout
