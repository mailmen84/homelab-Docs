# nginx (reverse proxy)

## Purpose
Reverse proxy / entrypoint for HTTP(S) services exposed on the homelab.

## Where it runs
- Type: VM on Proxmox (DL380).
- Host: `proxmox` (`192.168.20.10`).
- VM ID: **100**.
- Hostname: `nginx`.
- IP: `192.168.20.55` (VLAN 20).
- MAC: `bc:24:11:af:dc:19`.
- Guest OS: Ubuntu Server.

## Configuration overview
- Upstreams: TODO (list services proxied: Immich, Grafana, Ollama UI, OpenClaw UI, …).
- TLS: TODO (Let's Encrypt? self-signed? terminated here or upstream?).
- Hostnames / vhosts: TODO.

## Dependencies
- All HTTP services it fronts (Immich, Grafana, Prometheus, Ollama, OpenClaw, UniFi, Wazuh, …).
- DNS: Pi-hole (`192.168.10.2`) for local name resolution / overrides.

## Known issues / TODO
- TODO: declarative netplan config on `192.168.20.55`.
- TODO: capture the current site/vhost configuration (redacted) under `docs/90-attachments/`.
