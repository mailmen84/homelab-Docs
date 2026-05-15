# Raspberry Pi — Monitoring (Prometheus/Grafana planned)

## Purpose
Planned monitoring node for the homelab:
- Prometheus (metrics)
- Grafana (dashboards)
- (Optional later) Alertmanager

## Physical / connectivity
- Connected switch port: TODO
- Link speed: 1Gb (typical) / Wi-Fi (if used) — TODO
- Power: USB power supply — TODO

## Network
- Active VLANs in the homelab: **10 / 20 / 30** (`192.168.10.0/24`, `192.168.20.0/24`, `192.168.30.0/24`).
- Note: per `docs/20-network/network-summary.md`, Pi-hole appears at `192.168.10.2` (VLAN 10) and Grafana at `192.168.20.108` (VLAN 20). Confirm whether they run on this Pi.
- IP: TODO
- Hostname: TODO

## Planned services
- Prometheus: TODO (targets: Proxmox nodes, TrueNAS, pfSense, switch if possible)
- Grafana: TODO
- Exporters: node_exporter, smartctl_exporter (if relevant) — TODO

## TODO (data to collect)
- Model (Pi 3 / Pi 4 etc.)
- OS (Raspberry Pi OS / Ubuntu)
- Storage (microSD / SSD)
- Final placement VLAN (likely “services/management” VLAN once defined)
