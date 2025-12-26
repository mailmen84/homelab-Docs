# HOMELAB Docs (Markdown + Git)

This repository contains the documentation for my homelab: hardware, network, virtualization, storage, and services.
The goal is to keep a clear “single source of truth” so the whole lab can be rebuilt from scratch if needed.

## Rules (important)
- We work step by step. No long “10-step plans” at once.
- Every step must explain what we are doing and why.
- Every command must be explained (what it does and when to use it).
- Never store secrets in Git (passwords, tokens, private keys, VPN configs, backups).

## Current environment (quick snapshot)
- Devices: DL380 (Proxmox), R320 (Proxmox), R410 (TrueNAS), Buffalo BS-MP2012 switch, Raspberry Pi (Prometheus/Grafana planned), Buffalo TS-XL NAS, PCs.
- Current subnet (temporary): `192.168.8.0/24`
- VLAN plan: `10/20/30/40/50/60` (trunk on switch port 5; access ports as previously defined)
- Switch port map (current):
  - Port 1: ISP
  - Port 2: R320
  - Port 3: R410
  - Port 4: PC
  - Port 5: DL380 (10Gb, trunk)
  - Port 6: Buffalo NAS

## Repository structure
- `docs/00-overview/` — high-level architecture, decisions, summary
- `docs/10-hardware/` — inventory and per-device pages
- `docs/20-network/` — IP plan, VLANs, switch ports, firewall/VPN
- `docs/30-storage/` — TrueNAS, datasets/shares, backup strategy
- `docs/40-virtualization/` — Proxmox nodes, VM/CT templates, clusters
- `docs/50-services/` — applications and internal services
- `docs/60-runbooks/` — rebuild-from-zero, disaster recovery, common fixes
- `docs/90-attachments/` — images, diagrams, exported configs (no secrets)
- `scripts/` — helper notes/scripts (export commands, checklists)

## Start here
1. Architecture summary: `docs/00-overview/homelab-summary.md`
2. Network summary: `docs/20-network/network-summary.md`
