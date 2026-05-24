# Dell R730 — TrueNAS

## Purpose
Secondary storage host running **TrueNAS SCALE**.
Planned role: **Proxmox Backup Server (PBS)** target — to be confirmed.

## Physical / connectivity
- Connected switch port: TODO
- Link speed: TODO
- Notes: Buffalo BS-MP2012 port not yet documented.

## Network
- Active VLANs in the homelab: **10 / 20 / 30** (`192.168.10.0/24`, `192.168.20.0/24`, `192.168.30.0/24`).
- TrueNAS IP: `192.168.20.12` (VLAN 20).
- MAC: `00:0a:f7:86:6d:85`.
- Hostname: `truenas-r730`.
- iDRAC management IP: TODO (expected on VLAN 30).

## Hardware summary (fill later)
- CPU(s): TODO
- RAM: TODO
- Disks/RAID/HBA: TODO
- NICs: TODO

## Storage layout (to fill)
- Pool name(s): TODO
- VDEV layout (RAIDZ / mirror): TODO
- Datasets: TODO

## Role TODO
- Confirm whether this host stays as TrueNAS SCALE long-term or is repurposed for PBS.
- If PBS: document the Proxmox node it backs up, retention policy, and verification job schedule.
