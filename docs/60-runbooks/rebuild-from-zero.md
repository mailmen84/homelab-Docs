# Rebuild From Zero (Runbook)

> This runbook describes the correct rebuild order for the homelab.
> Authoritative target state: [`docs/00-overview/homelab-summary.md`](../00-overview/homelab-summary.md).

## Phase 1 — Hardware + cabling
- Verify power, cabling, switch port map (see [`docs/20-network/port-map.md`](../20-network/port-map.md)).
- Verify server boot and iLO/iDRAC access (DL380, R320, R410, R730).

## Phase 2 — Network baseline
- Confirm pfSense reachable on each active VLAN gateway:
  - VLAN 10 (`192.168.10.1`), VLAN 20 (`192.168.20.1`), VLAN 30 (`192.168.30.1`).
- Confirm DHCP pool and static leases on VLAN 20 (`192.168.20.100`–`192.168.20.200`, statics outside the pool).
- Confirm Pi-hole DNS at `192.168.10.2` and Tailscale subnet router at `192.168.10.130`.
- Confirm switch management access (Buffalo BS-MP2012 and the Cisco secondary switch).
- Confirm port map matches [`docs/20-network/port-map.md`](../20-network/port-map.md).
- Historical note: previously a flat `192.168.8.0/24` was used before VLAN segmentation; not part of the rebuild target.

## Phase 3 — Virtualization (Proxmox)
- DL380: install/verify **Proxmox VE 9.2.2** at `192.168.20.10`.
- Record Proxmox version, bridges, VLAN-aware setting, management IPs.
- R320: install/verify its operating system (**NOT** Proxmox — final role TBD; see [`docs/10-hardware/servers/dell-r320.md`](../10-hardware/servers/dell-r320.md)).

## Phase 4 — Storage (TrueNAS)
- R410 (`192.168.20.11`): TrueNAS SCALE. Verify pools, datasets, shares, permissions.
- R730 (`192.168.20.12`): TrueNAS SCALE. Verify pools, datasets, shares, permissions. Planned future role: PBS (TBC).
- PC Workstation (`192.168.20.13`): TrueNAS SCALE. Verify pools, datasets, shares, permissions.

## Phase 5 — Core services (VMs on Proxmox)
Bring up VMs in this order; see [`docs/40-virtualization/vm-inventory.md`](../40-virtualization/vm-inventory.md) for the full inventory.

1. **nginx** (VM 100, `192.168.20.55`) — reverse proxy / TLS entrypoint.
2. **Pi-hole** (Raspberry Pi `192.168.10.2`) — DNS must be online before dependent services.
3. **Prometheus** (VM 105, `192.168.20.52`) and **Grafana** (VM 102, `192.168.20.54`).
4. **Wazuh** (VM 109, `192.168.20.58`).
5. **Immich** (VM 103, `192.168.20.53`) — depends on TrueNAS NFS.
6. **Ollama** (VM 101, `192.168.20.57`) → **OpenClaw** (VM 108, `192.168.20.56`).
7. **UniFi controller** (VM 104, `192.168.20.59`).
8. **FitnessApp** + DB (VMs 106 / 107, `192.168.20.50` / `.51`).

## Phase 6 — Remaining VLAN rollout
- Define and enforce VLAN 40 / 50 / 60 in pfSense.
- Apply switch trunk/access configuration accordingly.
- Move user/IoT/guest devices off VLAN 20 onto their target VLANs.

## Verification checklist (high level)
- All management pages reachable (pfSense, Proxmox, TrueNAS x3, switches).
- Storage shares reachable from VMs (Immich → R410 NFS).
- Proxmox VMs reachable on their documented IPs.
- Pi-hole resolving for clients on all active VLANs.
- Grafana shows current Prometheus data.
