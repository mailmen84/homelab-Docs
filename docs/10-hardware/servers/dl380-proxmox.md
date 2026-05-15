# HPE ProLiant DL380 Gen9 — Proxmox

## Purpose
Main compute node running Proxmox. Planned trunk uplink to the switch for VLANs.

## Physical / connectivity
- Connected switch port: **Port 5**
- Link: **10Gb** (as currently planned/used)
- Switch notes: Port 5 planned as **trunk** carrying VLANs 10/20/30/40/50/60

## Network
- Active VLANs in the homelab: **10 / 20 / 30** (`192.168.10.0/24`, `192.168.20.0/24`, `192.168.30.0/24`).
- Proxmox node IP (per network-summary): `192.168.20.100` (VLAN 20). Confirm and update once verified.
- Management IP: TODO
- Hostname (FQDN): TODO

## Proxmox notes
- Cluster: TODO (standalone or cluster)
- Bridges: TODO (e.g., vmbr0, VLAN-aware yes/no)
- Storage used by Proxmox: TODO (local disks / ZFS / shared storage)

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
