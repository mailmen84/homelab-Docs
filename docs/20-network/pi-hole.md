# Pi-hole

## Purpose
Network-wide DNS sinkhole and (optionally) local DNS resolver.

## Where it runs
- Host: Raspberry Pi (also planned to host Prometheus/Grafana — see [`raspberry-pi-monitoring.md`](../10-hardware/servers/raspberry-pi-monitoring.md)).
- IP (per `network-summary.md`): `192.168.10.2` (VLAN 10 — Management / LAN).
- Admin UI: `http://192.168.10.2/admin`.

## DNS clients
- Is Pi-hole used by all devices (set as the DHCP-provided DNS in pfSense)? TODO.
- Per-VLAN DNS behavior: TODO (in particular, whether Servers/VLAN 20 hits Pi-hole).

## Upstream DNS
- Upstream resolver(s): TODO (e.g. `1.1.1.1`, `9.9.9.9`, or pfSense local resolver).
- DoH/DoT enabled? TODO.

## Local DNS records (homelab)
- Optional: define local A records (e.g. `pfsense.lab`, `truenas.lab`, `proxmox.lab`) — TODO.

## Blocklists
- Blocklists in use: TODO.
- Allowlist/exceptions: TODO.

## TODO (next steps)
1. Confirm whether pfSense hands out `192.168.10.2` as DNS server in DHCP scopes for VLAN 10/20/30.
2. Document upstream resolvers and any DoH/DoT setup.
3. List blocklists and any local DNS overrides.
4. Decide whether Pi-hole is acceptable as a single point of DNS failure or whether a secondary is needed.
