# IP Address Register

> Authoritative source for service/VM IPs: [`docs/00-overview/homelab-summary.md`](../00-overview/homelab-summary.md)
> and [`network-summary.md`](network-summary.md).
> This page lists the **physical devices** and their primary management IP.

## Active subnets (VLANs in service)
- **VLAN 10 — Infrastructure (Pi-hole, Tailscale):** `192.168.10.0/24`
- **VLAN 20 — Servers, VMs, services:** `192.168.20.0/24`
- **VLAN 30 — Hardware management (iDRAC/iLO, IoT):** `192.168.30.0/24`

Planned (not yet enforced): VLAN 40 / 50 / 60.

> Historical: an earlier flat `192.168.8.0/24` was used before VLAN segmentation. It is no longer active.

## Address allocation in `192.168.20.0/24`
| Range | Use |
|---|---|
| `.1` | pfSense (gateway / DHCP) |
| `.2–.9` | Infrastructure reserved |
| `.10–.49` | Physical servers |
| `.50–.99` | Proxmox VMs |
| `.100–.200` | DHCP pool |
| `.201–.254` | Reserved |

## Physical devices

| Device | Role | Hostname | IP | VLAN | MAC | Switch port | Notes |
|---|---|---|---|---|---|---|---|
| HPE DL380 Gen9 | Proxmox VE 9.2.2 | proxmox | `192.168.20.10` | 20 (trunk planned) | `9c:dc:71:6e:14:28` | 5 (10Gb) | Main compute |
| Dell R320 | TBD (**not** Proxmox) | TODO | TODO | TBD | TODO | 2 | Role to be confirmed |
| Dell R410 | TrueNAS SCALE | truenas-r410 | `192.168.20.11` | 20 | `5c:ed:8c:1b:6e:6c` | 3 | Primary storage |
| Dell R730 | TrueNAS SCALE (planned PBS — TBC) | truenas-r730 | `192.168.20.12` | 20 | `00:0a:f7:86:6d:85` | TBD | |
| PC Workstation | TrueNAS SCALE | truenas-pc | `192.168.20.13` | 20 | `04:d9:f5:0f:43:ca` | TBD | |
| Buffalo BS-MP2012 | Core switch (managed) | TODO | TODO | mgmt | TODO | n/a | |
| Cisco switch | Secondary switch (managed) | TODO | TODO | mgmt | TODO | TBD | VLAN-capable |
| Buffalo TS-XL NAS | Secondary NAS | TODO | TODO | TBD | TODO | 6 | |
| Raspberry Pi 3+ | Pi-hole DNS | pihole | `192.168.10.2` | 10 | `b8:27:eb:24:82:e7` | TBD | Planned Prometheus/Grafana node |
| Dell Optiplex | Tailscale subnet router | tailscale | `192.168.10.130` | 10 | `64:00:6a:97:3d:b3` | TBD | Static lease set |
| pfSense (PC, 2× NIC) | Firewall/router | pfsense | `192.168.10.1` (VLAN 10 gw); `192.168.20.1` (VLAN 20 gw); `192.168.30.1` (VLAN 30 gw) | trunk | TODO | TBD | Bare metal; DHCP server |
| UniFi AP Pro | Wi-Fi AP | TODO | TODO | TBD | TODO | TBD | VLAN mapping TBD |
| PC (main) | Client | TODO | DHCP | 20 (DHCP pool) | TODO | 4 | |

## Proxmox VMs (DL380 / `192.168.20.10`)
See [`docs/40-virtualization/vm-inventory.md`](../40-virtualization/vm-inventory.md) for the authoritative VM list.

## DHCP
- DHCP server: pfSense (per VLAN).
- VLAN 20 pool: `192.168.20.100`–`192.168.20.200` with static leases by MAC for every documented device.
- VLAN 10 / VLAN 30 pools: TODO.
