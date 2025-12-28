# Rebuild From Zero (Runbook)

> This runbook describes the correct rebuild order for the homelab.
> Detailed steps will be added gradually as the lab stabilizes.

## Phase 1 — Hardware + cabling
- Verify power, cabling, switch port map
- Verify server boot and iLO/iDRAC access (if applicable)

## Phase 2 — Network baseline
- Confirm temporary LAN access (192.168.8.0/24)
- Confirm switch management access
- Confirm port map matches `docs/20-network/port-map.md`

## Phase 3 — Virtualization (Proxmox)
- DL380: install/verify Proxmox
- R320: install/verify Proxmox
- Record Proxmox versions, bridges, IPs

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
