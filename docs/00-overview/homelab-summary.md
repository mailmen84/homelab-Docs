# Homelab Summary

## Purpose
This documentation describes my homelab so it can be maintained and rebuilt reliably.
Current focus: keeping documentation in sync with the active VLAN-segmented network and filling in the remaining gaps.

## Current devices (high-level)
- **DL380** — Proxmox (main compute; 10Gb uplink to switch on port 5)
- **R320** — role TBD (**not** Proxmox)
- **R410** — TrueNAS (primary storage)
- **R730** - TrueNAS (PBS tbc
- **Buffalo BS-MP2012** — switch (core connectivity)
- **Raspberry Pi** — Pi-hole / planned Prometheus/Grafana node
- **Buffalo TS-XL NAS** — secondary NAS
- **PCs** — user endpoints / management access

## Physical topology (current)
Switch port map (Buffalo BS-MP2012):
- Port 1: ISP / internet uplink
- Port 2: R320 (not Proxmox)
- Port 3: R410 (TrueNAS)
- Port 4: PC
- Port 5: DL380 (Proxmox, 10Gb, trunk planned)
- Port 6: Buffalo NAS (TS-XL)

## Network status (current)
- Current active subnets (via VLANs): **192.168.10.0/24 (VLAN 10), 192.168.20.0/24 (VLAN 20), 192.168.30.0/24 (VLAN 30)**
- VLAN plan (target): **10 / 20 / 30 / 40 / 50 / 60**
- Trunk plan: **port 5** (DL380 uplink/trunk)
- Access ports: as previously defined (to be documented explicitly in the Network chapter)

## Next documentation tasks
- Define VLAN names/purposes and map them to switch ports (explicit table).
- Document Proxmox networking (bridges, VLAN awareness, management IPs).
- Document TrueNAS shares and dataset structure.
- Add a “rebuild-from-zero” runbook as the system stabilizes.
# Homelab Network Configuration

> Document generated: 2026-05-24  
> Status: In progress — pfSense and physical servers completed ✅

---

## Network topology

| Subnet | VLAN | Purpose |
|---|---|---|
| `192.168.10.0/24` | VLAN 10 | Network infrastructure (Pi-hole, Tailscale) |
| `192.168.20.0/24` | VLAN 20 | Servers, VMs, services |
| `192.168.30.0/24` | VLAN 30 | Hardware management (iDRAC/iLO, IoT) |

---

## IP addressing — plan

### Ranges in `192.168.20.x`

| Range | Purpose |
|---|---|
| `.1` | pfSense — gateway / DHCP server |
| `.2–.9` | Reserved (infrastructure) |
| `.10–.49` | Physical servers |
| `.50–.99` | Proxmox VMs |
| `.100–.200` | DHCP pool (dynamic) |
| `.201–.254` | Reserved |

---

## Physical servers

| IP | Hostname | Hardware | Role | MAC address | Status |
|---|---|---|---|---|---|
| `192.168.20.10` | proxmox | HP DL380 Gen9 | Proxmox VE 9.2.2 | `9c:dc:71:6e:14:28` | ✅ configured |
| `192.168.20.11` | truenas-r410 | Dell R410 | TrueNAS Scale | `5c:ed:8c:1b:6e:6c` | ✅ configured |
| `192.168.20.12` | truenas-r730 | Dell R730 | TrueNAS Scale | `00:0a:f7:86:6d:85` | ✅ configured |
| `192.168.20.13` | truenas-pc | PC Workstation | TrueNAS Scale | `04:d9:f5:0f:43:ca` | ✅ configured |

### Network infrastructure `192.168.10.x`

| IP | Hostname | Hardware | Role | MAC address | Status |
|---|---|---|---|---|---|
| `192.168.10.2` | pihole | Raspberry Pi 3+ | Pi-hole DNS | `b8:27:eb:24:82:e7` | ✅ configured |
| `192.168.10.130` | tailscale | Dell Optiplex | Tailscale VPN | `64:00:6a:97:3d:b3` | ⏳ static lease set |

---

## Proxmox VMs

All VMs run on **HP DL380 Gen9 (Proxmox `192.168.20.10`)**, OS: Ubuntu Server.

| IP | Hostname | Proxmox VM ID | Role | MAC address | Netplan status |
|---|---|---|---|---|---|
| `192.168.20.50` | fitnessapp | VM 106 | Diet app | `bc:24:11:20:b1:eb` | ⏳ IP hardcoded |
| `192.168.20.51` | fitnessapp-db | VM 107 | fitnessapp database | `bc:24:11:04:31:6d` | ⏳ IP hardcoded |
| `192.168.20.52` | prometheus | VM 105 | Monitoring metrics | `bc:24:11:97:e9:2c` | ⏳ needs configuration |
| `192.168.20.53` | immich | VM 103 | Photo management | `bc:24:11:c9:b5:18` | ⏳ needs configuration |
| `192.168.20.54` | grafana | VM 102 | Monitoring dashboard | `bc:24:11:d1:5a:82` | ⏳ needs configuration |
| `192.168.20.55` | nginx | VM 100 | Reverse proxy | `bc:24:11:af:dc:19` | ⏳ needs configuration |
| `192.168.20.56` | openclaw | VM 108 | — | `bc:24:11:27:2d:72` | ⏳ needs configuration |
| `192.168.20.57` | ollama | VM 101 | AI / LLM | `bc:24:11:a8:b3:f9` | ⏳ needs configuration |
| `192.168.20.58` | wazuh | VM 109 | SIEM / Security | `bc:24:11:92:4e:5b` | ⏳ needs configuration |
| `192.168.20.59` | unifi | VM 104 | UniFi Controller | `bc:24:11:2d:82:1a` | ⏳ needs configuration |

---

## pfSense — DHCP configuration

- **Address Pool Range:** `192.168.20.100` → `192.168.20.200`
- Static leases assigned for all devices listed above based on MAC address

---

## Proxmox — `/etc/network/interfaces`

