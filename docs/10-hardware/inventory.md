# Hardware Inventory

> Authoritative source for the device list: [`docs/00-overview/homelab-summary.md`](../00-overview/homelab-summary.md).
> Per-device details live in separate pages under `docs/10-hardware/`.

## Core compute
- **HPE ProLiant DL380 Gen9** — Proxmox VE 9.2.2; `192.168.20.10`; 10Gb uplink, planned trunk on switch port 5. See [`servers/dl380-proxmox.md`](servers/dl380-proxmox.md).
- **Dell R320** — role TBD (**not** Proxmox); switch port 2. See [`servers/dell-r320.md`](servers/dell-r320.md).

## Storage / NAS
- **Dell R410** — TrueNAS SCALE; `192.168.20.11`; switch port 3. See [`servers/r410-truenas.md`](servers/r410-truenas.md).
- **Dell R730** — TrueNAS SCALE (planned PBS — TBC); `192.168.20.12`. See [`servers/r730-truenas.md`](servers/r730-truenas.md).
- **PC Workstation** — TrueNAS SCALE; `192.168.20.13`. See [`servers/pc-truenas.md`](servers/pc-truenas.md).
- **Buffalo TS-XL NAS** — secondary NAS; switch port 6. See [`servers/buffalo-tsxl-nas.md`](servers/buffalo-tsxl-nas.md).

## Network
- **Buffalo BS-MP2012** — managed core switch. See [`network/switch.md`](network/switch.md).
- **Cisco switch** — secondary managed switch (VLAN-capable); details TBD.
- **UniFi AP Pro** — wireless access point; VLAN mapping TBD.
- **pfSense (PC, 2× NICs)** — bare-metal firewall/router. See [`docs/20-network/pfsense.md`](../20-network/pfsense.md).

## Monitoring / utility
- **Raspberry Pi 3+** — Pi-hole DNS (`192.168.10.2`); planned Prometheus + Grafana host. See [`servers/raspberry-pi-monitoring.md`](servers/raspberry-pi-monitoring.md).
- **Dell Optiplex** — Tailscale subnet router (`192.168.10.130`).

## Endpoints
- **PC(s)** — management / client access (currently connected on switch port 4).
