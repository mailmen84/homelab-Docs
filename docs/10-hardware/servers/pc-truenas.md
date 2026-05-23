# PC Workstation — TrueNAS

## Purpose
Repurposed PC workstation running **TrueNAS SCALE** as an additional storage host.

## Physical / connectivity
- Connected switch port: TODO
- Link speed: TODO (audit form mentions a 10Gb NIC option)
- Notes: switch port on Buffalo BS-MP2012 not yet documented.

## Network
- Active VLANs in the homelab: **10 / 20 / 30** (`192.168.10.0/24`, `192.168.20.0/24`, `192.168.30.0/24`).
- TrueNAS IP: `192.168.20.13` (VLAN 20).
- MAC: `04:d9:f5:0f:43:ca`.
- Hostname: `truenas-pc`.

## Hardware summary
- CPU: Intel i5 (per audit form)
- RAM: 32 GB DDR3 (per audit form)
- NICs: 10 Gb (per audit form)
- Disks/RAID/HBA: TODO

## Storage layout (to fill)
- Pool name(s): TODO
- VDEV layout: TODO
- Datasets: TODO

## TODO
- Confirm exact role of this host versus the R410 and R730 (primary / secondary / scratch).
- Document switch port and link speed.
- Capture pool, dataset, and share configuration.
