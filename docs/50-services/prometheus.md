# Prometheus

## Purpose
Metrics collection / time-series database for the homelab.

## Where it runs
- Type: VM on Proxmox (DL380).
- Host: `proxmox` (`192.168.20.10`).
- VM ID: **105**.
- Hostname: `prometheus`.
- IP: `192.168.20.52` (VLAN 20).
- MAC: `bc:24:11:97:e9:2c`.
- Guest OS: Ubuntu Server.

## Configuration overview
- Scrape targets: TODO (DL380 node_exporter, TrueNAS hosts, pfSense, switch if possible).
- Retention policy: TODO.
- Storage location: TODO.

## Dependencies
- Consumed by [Grafana](grafana.md) at `192.168.20.54`.
- DNS: Pi-hole (`192.168.10.2`).

## Known issues / TODO
- Per audit form: no current issues.
- TODO: declarative netplan config on `192.168.20.52`.
- TODO: list exporter endpoints per server in this page.
