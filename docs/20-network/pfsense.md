# pfSense

## Purpose
Primary firewall and router for the homelab. Bare-metal PC with 2× NICs.
Gateway on every active VLAN; runs DHCP and DNS for the lab.

## Where it runs
- Hardware: PC with 2× NICs (bare metal).
- IP (per `network-summary.md`): `192.168.10.1` (VLAN 10 gateway). Also `192.168.20.1` (VLAN 20 gw), `192.168.30.1` (VLAN 30 gw).
- Web UI: `https://192.168.10.1:8443` (per current nmap scan; confirm exact port).

## Interfaces (to fill)
| Interface | Type | Role | VLAN(s) | Notes |
|---|---|---|---|---|
| WAN | physical NIC | uplink to ISP | — | TODO |
| LAN trunk | physical NIC | trunk to switch | 10/20/30 (+40/50/60 planned) | TODO |

## VLANs served
- **VLAN 10** — Management / LAN: `192.168.10.0/24`, gw `192.168.10.1`.
- **VLAN 20** — Servers: `192.168.20.0/24`, gw `192.168.20.1`.
- **VLAN 30** — Mgmt / iLO / Other: `192.168.30.0/24`, gw `192.168.30.1`.
- Planned: VLAN 40 / 50 / 60.

## DHCP (to fill)
- DHCP scope per VLAN: TODO.
- Static reservations: TODO (see [`ip-register.md`](ip-register.md)).

## DNS
- Upstream DNS: TODO (often Pi-hole at `192.168.10.2` as a forwarder).
- DNS resolver vs forwarder mode: TODO.
- Domain overrides / local DNS records: TODO.

## Firewall rules (high level, no secrets)
- WAN → LAN: TODO (default deny in, allow established).
- Inter-VLAN: TODO (per user's "security is the main concern" — needs explicit per-VLAN policy).
- Specific allowances (management access, Tailscale subnet): TODO.

## VPN / Tailscale
- Tailscale subnet router runs on Ubuntu/NUC `192.168.10.130`, advertising VLAN routes.
- pfSense-side VPN (OpenVPN / WireGuard / IPsec): TODO.

## Backup / config export
- pfSense config XML backup location: TODO (must be redacted before any commit — see [`docs/90-attachments/README.md`](../90-attachments/README.md)).
- Schedule: TODO.

## TODO (next steps)
1. Capture interface names and confirm WAN/LAN NICs.
2. Document DHCP scopes for VLAN 10/20/30.
3. Capture firewall rule summary per VLAN.
4. Export a redacted copy of the config to `docs/90-attachments/configs-redacted/`.
