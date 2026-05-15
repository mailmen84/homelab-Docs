# HOMELAB Docs (Markdown + Git)

This repository contains the documentation for my homelab: hardware, network, virtualization, storage, and services.
The goal is to keep a clear "single source of truth" so the whole lab can be rebuilt from scratch if needed.

## Rules (important)
- We work step by step. No long "10-step plans" at once.
- Every step must explain what we are doing and why.
- Every command must be explained (what it does and when to use it).
- Never store secrets in Git (passwords, tokens, private keys, VPN configs, backups).
- See [CONVENTIONS.md](CONVENTIONS.md) for file naming, structure, and page format.

## Current environment (quick snapshot)
- Devices: DL380 (Proxmox), R320 (role TBD, **not** Proxmox), R410 (TrueNAS), Buffalo BS-MP2012 switch, Raspberry Pi, Buffalo TS-XL NAS, PCs.
- **Active subnets (VLANs already in service):**
  - `192.168.10.0/24` — VLAN 10 (Management / LAN)
  - `192.168.20.0/24` — VLAN 20 (Servers)
  - `192.168.30.0/24` — VLAN 30 (Management / iLO / Other)
- VLAN plan (target): `10/20/30/40/50/60` — VLANs 40/50/60 are planned, not yet enforced.
- Switch port map (current cabling):
  - Port 1: ISP
  - Port 2: R320
  - Port 3: R410
  - Port 4: PC
  - Port 5: DL380 (10Gb, planned trunk)
  - Port 6: Buffalo NAS
- Historical note: the lab previously used a flat `192.168.8.0/24` network before VLAN segmentation; pages that still mention it are being migrated.

## Repository structure
- `docs/00-overview/` — high-level architecture, decisions, summary
- `docs/10-hardware/` — inventory and per-device pages
- `docs/20-network/` — IP plan, VLANs, switch ports, pfSense, Pi-hole
- `docs/30-storage/` — TrueNAS, datasets/shares, backup strategy
- `docs/40-virtualization/` — Proxmox nodes, VM/CT templates, clusters
- `docs/50-services/` — applications and internal services (incl. OpenClaw AI)
- `docs/60-runbooks/` — rebuild-from-zero, disaster recovery, common fixes
- `docs/70-security/` — secrets management, access control, backups
- `docs/90-attachments/` — images, diagrams, exported configs (no secrets)
- `docs/CCNA_Study_Notes/` — study material (not homelab configuration)
- `forms/` — questionnaires and audit forms
- `scripts/` — helper notes/scripts (export commands, checklists)

## Start here
1. Network summary (most up-to-date): `docs/20-network/network-summary.md`
2. Architecture summary: `docs/00-overview/homelab-summary.md`
3. Hardware inventory: `docs/10-hardware/inventory.md`
