# HPE ProLiant DL380 Gen9 — Proxmox

## Purpose
Main compute node running Proxmox. Planned trunk uplink to the switch for VLANs.

## Physical / connectivity
- Connected switch port: **Port 2**
- Link: **1Gb** (as currently planned/used)
- Switch notes: Port 2 planned as **trunk** carrying VLANs 10/20/30/40/50/60

## Current network (temporary)
- Subnet (temporary): **192.168.8.0/24**
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
