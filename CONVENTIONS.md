# Conventions

This document captures the rules for organizing and writing the homelab documentation.
Follow these so new pages stay consistent with existing ones.

## File and directory naming
- Use **kebab-case** for file and directory names: `dell-r320.md`, `port-map.md`, `60-runbooks/`.
- All documentation files use the `.md` extension. No bare-name files in `docs/`.
- Keep names in English unless a folder is explicitly for non-English material (e.g. study notes).
- Server pages are named after the device, not its role: `dell-r320.md`, **not** `r320-proxmox.md` (roles change; hardware names do not).

## Numbered documentation sections (`docs/`)
- `00-overview/` — high-level architecture, summary, decisions (ADRs).
- `10-hardware/` — inventory and per-device pages (`servers/`, `network/`).
- `20-network/` — IP plan, VLANs, switch ports, pfSense, Pi-hole, Tailscale.
- `30-storage/` — TrueNAS pools, datasets, shares, snapshots, replication.
- `40-virtualization/` — Proxmox cluster, bridges/VLANs, VM/CT inventory.
- `50-services/` — applications and internal services (Immich, Grafana, OpenClaw AI, …).
- `60-runbooks/` — operational procedures and troubleshooting.
- `70-security/` — secrets handling, access control, backups.
- `90-attachments/` — diagrams, exported (redacted) configs, images.

Each section should have a `README.md` acting as a one-page index.

## Source of truth for network state
- The **authoritative current state** of the network lives in:
  - [`docs/20-network/network-summary.md`](docs/20-network/network-summary.md) — services, IPs, URLs.
  - [`docs/20-network/ip-register.md`](docs/20-network/ip-register.md) — device IP register.
- Active subnets today: `192.168.10.0/24` (VLAN 10), `192.168.20.0/24` (VLAN 20), `192.168.30.0/24` (VLAN 30).
- The earlier flat `192.168.8.0/24` is **historical only** and must not be presented as the current network.

## Secrets — never in Git
Never commit:
- passwords, API tokens, SSH/TLS private keys (`*.key`, `*.pem`, `*.pfx`),
- `.env` files with real values,
- VPN configuration files containing credentials,
- backup archives.

Use a password manager for credentials. Use the placeholder `TODO` in docs where a credential or secret detail would otherwise appear.

## Page format

### Hardware page (`docs/10-hardware/servers/*.md`)
Sections, in order:
1. `# <Device name>` — title is the hardware identifier, not the role.
2. `## Purpose` — what the device does in the lab.
3. `## Physical / connectivity` — switch port, link speed, trunk/access.
4. `## Network` — VLAN assignment, IP, hostname.
5. `## Hardware summary` — CPU, RAM, disks, NICs.
6. Role-specific section (e.g. `## Proxmox notes`, `## Storage layout`).
7. `## TODO` — list of unknowns to fill in.

### Service page (`docs/50-services/*.md`)
Sections, in order:
1. `# <Service name>` — official product name.
2. `## Purpose` — what it does for the lab.
3. `## Where it runs` — host, VM/CT, IP, VLAN.
4. `## Configuration overview` — relevant settings (no secrets).
5. `## Dependencies` — what it needs (storage, DNS, other services).
6. `## Known issues / TODO`.

### Runbook (`docs/60-runbooks/*.md`)
- Start with a one-line summary of the problem or procedure.
- Use numbered steps. Each command must be explained (what it does, why).
- Note the final verification step.

## Commit messages
- Use short imperative subject lines (e.g. `Rename r320-proxmox.md to dell-r320.md`).
- One logical change per commit when feasible.
