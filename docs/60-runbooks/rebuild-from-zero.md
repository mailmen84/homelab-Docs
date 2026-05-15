# Rebuild From Zero (Runbook)

> This runbook describes the correct rebuild order for the homelab.
> Detailed steps will be added gradually as the lab stabilizes.

## Phase 1 — Hardware + cabling
- Verify power, cabling, switch port map
- Verify server boot and iLO/iDRAC access (if applicable)

## Phase 2 — Network baseline
- Confirm pfSense reachable on each active VLAN gateway:
  - VLAN 10 (`192.168.10.1`), VLAN 20 (`192.168.20.1`), VLAN 30 (`192.168.30.1`).
- Confirm switch management access (Buffalo BS-MP2012 and any additional switch).
- Confirm port map matches `docs/20-network/port-map.md`.
- Historical note: previously a flat `192.168.8.0/24` was used before VLAN segmentation; not part of the rebuild target.

## Phase 3 — Virtualization (Proxmox)
- DL380: install/verify Proxmox
- Record Proxmox versions, bridges, IPs
- R320: install/verify its operating system (NOT Proxmox — final role TBD; see `docs/10-hardware/servers/dell-r320.md`)

## Phase 4 — Storage (TrueNAS)
- R410: install/verify TrueNAS
- Verify pools, datasets, shares
- Verify permissions model

## Phase 5 — Core services
- Monitoring node (Raspberry Pi): Prometheus + Grafana (planned)
- Other services (planned): TODO

## Phase 6 — VLAN rollout (planned)
- Implement pfSense (separate project/chat)
- Define VLAN interfaces + IP ranges
- Apply switch trunk/access configuration
- Migrate devices from flat LAN to VLANs

## Verification checklist (high level)
- All management pages reachable
- Storage shares reachable
- Proxmox nodes reachable
- Basic monitoring up
