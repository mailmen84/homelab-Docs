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

## Current network (temporary)
- Subnet (temporary): **192.168.8.0/24**
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
