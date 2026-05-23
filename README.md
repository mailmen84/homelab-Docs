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
> Authoritative source: [`docs/00-overview/homelab-summary.md`](docs/00-overview/homelab-summary.md).

- Devices:
  - **HPE DL380 Gen9** — Proxmox VE 9.2.2 (main compute, 10Gb uplink to switch port 5)
  - **Dell R320** — role TBD (**not** Proxmox)
  - **Dell R410** — TrueNAS SCALE (primary storage)
  - **Dell R730** — TrueNAS SCALE (planned PBS — to be confirmed)
  - **PC Workstation** — TrueNAS SCALE (additional storage host)
  - **Buffalo BS-MP2012** — managed switch (core connectivity)
  - **Cisco switch** — secondary managed switch (VLAN-capable; details TBD)
  - **Raspberry Pi 3+** — Pi-hole DNS (planned Prometheus/Grafana node)
  - **Buffalo TS-XL NAS** — secondary NAS
  - **Dell Optiplex (Ubuntu/NUC-style)** — Tailscale subnet router
  - **PC(s)** — user endpoints / management access
- **Active subnets (VLANs already in service):**
  - `192.168.10.0/24` — VLAN 10 (Infrastructure: Pi-hole, Tailscale)
  - `192.168.20.0/24` — VLAN 20 (Servers, VMs, services)
  - `192.168.30.0/24` — VLAN 30 (Hardware management: iDRAC/iLO, IoT)
- VLAN plan (target): `10/20/30/40/50/60` — VLANs 40/50/60 are planned, not yet enforced.
- Switch port map (current cabling, Buffalo BS-MP2012):
  - Port 1: ISP
  - Port 2: Dell R320 (**not** Proxmox)
  - Port 3: Dell R410 (TrueNAS)
  - Port 4: PC
  - Port 5: HPE DL380 (Proxmox, 10Gb, planned trunk)
  - Port 6: Buffalo TS-XL NAS
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
1. Homelab summary (source of truth): `docs/00-overview/homelab-summary.md`
2. Network summary: `docs/20-network/network-summary.md`
3. Hardware inventory: `docs/10-hardware/inventory.md`
4. VM inventory: `docs/40-virtualization/vm-inventory.md`
