# Grafana

## Purpose
Visualisation and dashboards for monitoring data collected by Prometheus.

## Where it runs
- Type: VM on Proxmox (DL380).
- Host: `proxmox` (`192.168.20.10`).
- VM ID: **102**.
- Hostname: `grafana`.
- IP: `192.168.20.54` (VLAN 20).
- MAC: `bc:24:11:d1:5a:82`.
- Guest OS: Ubuntu Server.
- Default Grafana UI port: `3000` (confirm).

## Configuration overview
- Data sources: Prometheus (`192.168.20.52`).
- Dashboards: TODO (list which dashboards are installed).
- Auth backend: TODO.

## Dependencies
- [Prometheus](prometheus.md) as the primary data source.
- DNS: Pi-hole (`192.168.10.2`).

## Known issues / TODO
- Per audit form: no current issues reported.
- TODO: confirm declarative netplan config is applied on `192.168.20.54`.
- TODO: list and version-control dashboard JSON exports under `docs/90-attachments/`.
