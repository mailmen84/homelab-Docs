# Raspberry Pi — Monitoring (Prometheus/Grafana planned)

## Purpose
Raspberry Pi 3+ serving as the lab's **Pi-hole DNS** sinkhole.
Originally also planned as a Prometheus / Grafana host; that role has since moved to dedicated VMs on Proxmox.

## Physical / connectivity
- Connected switch port: TODO
- Link speed: 1Gb (typical) / Wi-Fi (if used) — TODO
- Power: USB power supply — TODO

## Network
- Active VLANs in the homelab: **10 / 20 / 30** (`192.168.10.0/24`, `192.168.20.0/24`, `192.168.30.0/24`).
- IP: `192.168.10.2` (VLAN 10 — Infrastructure).
- MAC: `b8:27:eb:24:82:e7`.
- Hostname: `pihole`.
- Note: Grafana and Prometheus currently run as separate **VMs on Proxmox** (`192.168.20.54` and `192.168.20.52`), not on this Pi. The "Prometheus/Grafana on Pi" plan is no longer the active deployment — keep this page in case the role changes later.

## Planned services
- Prometheus: TODO (targets: Proxmox nodes, TrueNAS, pfSense, switch if possible)
- Grafana: TODO
- Exporters: node_exporter, smartctl_exporter (if relevant) — TODO

## TODO (data to collect)
- Model (Pi 3 / Pi 4 etc.)
- OS (Raspberry Pi OS / Ubuntu)
- Storage (microSD / SSD)
- Final placement VLAN (likely “services/management” VLAN once defined)
