# Network Summary (Current + Planned)

> Authoritative source for the homelab state: [`docs/00-overview/homelab-summary.md`](../00-overview/homelab-summary.md).
> This page is the network-focused view of that summary.

## Current state (active VLANs)
- Active subnets:
  - **192.168.10.0/24** (VLAN 10 — Infrastructure: Pi-hole, Tailscale)
  - **192.168.20.0/24** (VLAN 20 — Servers, VMs, services)
  - **192.168.30.0/24** (VLAN 30 — Hardware management: iDRAC/iLO, IoT)
- Notes:
  - VLAN segmentation is active and enforced by pfSense.
  - Tailscale subnet router (Dell Optiplex `192.168.10.130`) advertises these routes.

## VLAN segmentation overview
| VLAN ID | Subnet | Purpose |
|---:|---|---|
| 10 | `192.168.10.0/24` | Infrastructure (Pi-hole, Tailscale) |
| 20 | `192.168.20.0/24` | Servers, VMs, services |
| 30 | `192.168.30.0/24` | Hardware management (iDRAC/iLO, IoT) |
| 40 | TBD | Planned |
| 50 | TBD | Planned |
| 60 | TBD | Planned |

See [`vlan-plan.md`](vlan-plan.md) for the planned VLAN names/purposes.

## Switch: Buffalo BS-MP2012 (port map)
Current cabling / port usage:
- Port 1: ISP (internet uplink)
- Port 2: Dell R320 (**not** Proxmox)
- Port 3: Dell R410 (TrueNAS)
- Port 4: PC
- Port 5: HPE DL380 (Proxmox, **10Gb**, planned **trunk**)
- Port 6: Buffalo TS-XL NAS

> R730 (TrueNAS) and PC Workstation (TrueNAS) are part of the lab but their switch ports are not yet documented — TODO.

## Trunk vs access (planned)
- Port 5: **Trunk** (VLANs 10/20/30/40/50/60) to DL380
- Other ports: **Access** ports (as previously defined)
  - TODO: write the explicit "port → VLAN" access table once confirmed

## IP plan — `192.168.20.x` (Servers VLAN)
| Range | Purpose |
|---|---|
| `.1` | pfSense — gateway / DHCP server |
| `.2–.9` | Reserved (infrastructure) |
| `.10–.49` | Physical servers |
| `.50–.99` | Proxmox VMs |
| `.100–.200` | DHCP pool (dynamic) |
| `.201–.254` | Reserved |

pfSense DHCP pool: `192.168.20.100`–`192.168.20.200`. Static leases are assigned by MAC for every device below.

## Active service IP addresses

### VLAN 10 — Infrastructure
| Service | IP | Hostname | URL / Access | Notes |
|---|---:|---|---|---|
| Gateway / pfSense | `192.168.10.1` | pfsense | `https://192.168.10.1` | bare-metal pfSense, gateway on all VLANs |
| Pi-hole | `192.168.10.2` | pihole | `http://192.168.10.2/admin` | Raspberry Pi 3+ (MAC `b8:27:eb:24:82:e7`) |
| Tailscale subnet router | `192.168.10.130` | tailscale | `ssh 192.168.10.130` | Dell Optiplex (MAC `64:00:6a:97:3d:b3`); static lease set |

### VLAN 20 — Servers (physical hosts)
| IP | Hostname | Hardware | Role | MAC | Status |
|---|---|---|---|---|---|
| `192.168.20.1` | pfsense | PC (2× NIC) | pfSense gateway VLAN 20 | TODO | ✅ |
| `192.168.20.10` | proxmox | HPE DL380 Gen9 | Proxmox VE 9.2.2 | `9c:dc:71:6e:14:28` | ✅ |
| `192.168.20.11` | truenas-r410 | Dell R410 | TrueNAS SCALE | `5c:ed:8c:1b:6e:6c` | ✅ |
| `192.168.20.12` | truenas-r730 | Dell R730 | TrueNAS SCALE (planned PBS — TBC) | `00:0a:f7:86:6d:85` | ✅ |
| `192.168.20.13` | truenas-pc | PC Workstation | TrueNAS SCALE | `04:d9:f5:0f:43:ca` | ✅ |

### VLAN 20 — Servers (Proxmox VMs on DL380)
All VMs run on **HPE DL380 Gen9** (Proxmox `192.168.20.10`), OS: Ubuntu Server.

| IP | Hostname | VM ID | Role | MAC | Netplan status |
|---|---|---:|---|---|---|
| `192.168.20.50` | fitnessapp | 106 | Diet/fitness application | `bc:24:11:20:b1:eb` | ⏳ IP hard-coded |
| `192.168.20.51` | fitnessapp-db | 107 | Database for fitnessapp | `bc:24:11:04:31:6d` | ⏳ IP hard-coded |
| `192.168.20.52` | prometheus | 105 | Monitoring metrics | `bc:24:11:97:e9:2c` | ⏳ to configure |
| `192.168.20.53` | immich | 103 | Photo management | `bc:24:11:c9:b5:18` | ⏳ to configure |
| `192.168.20.54` | grafana | 102 | Monitoring dashboards | `bc:24:11:d1:5a:82` | ⏳ to configure |
| `192.168.20.55` | nginx | 100 | Reverse proxy | `bc:24:11:af:dc:19` | ⏳ to configure |
| `192.168.20.56` | openclaw | 108 | OpenClaw AI | `bc:24:11:27:2d:72` | ⏳ to configure |
| `192.168.20.57` | ollama | 101 | AI / LLM runtime | `bc:24:11:a8:b3:f9` | ⏳ to configure |
| `192.168.20.58` | wazuh | 109 | SIEM / security | `bc:24:11:92:4e:5b` | ⏳ to configure |
| `192.168.20.59` | unifi | 104 | UniFi controller | `bc:24:11:2d:82:1a` | ⏳ to configure |

### VLAN 30 — Hardware management (iDRAC/iLO, IoT)
TODO — populate once iDRAC/iLO addresses are confirmed on this VLAN.
Per `homelab-summary.md`, VLAN 30 is dedicated to hardware management interfaces (iDRAC/iLO) and IoT.

## pfSense — DHCP
- Address pool range: `192.168.20.100` → `192.168.20.200`
- Static leases assigned for every device listed above on the basis of MAC address.

## Proxmox — `/etc/network/interfaces`
TODO — capture the running configuration from DL380 (`192.168.20.10`).
See [`docs/40-virtualization/`](../40-virtualization/) for bridge/VLAN-aware setup once documented.

## Next steps for documentation
- Capture VLAN 30 IPs for iDRAC/iLO and any IoT devices.
- Define VLAN 40/50/60 names/purposes in [`vlan-plan.md`](vlan-plan.md).
- Document switch port mapping: explicit "port → VLAN" access table once R730 and PC-TrueNAS ports are known.
- Document Proxmox networking (bridges, VLAN awareness, management IPs).
- Document TrueNAS shares and dataset structure (per host: R410, R730, PC-TrueNAS).
- Migrate all `⏳` VM netplan entries to declarative static configuration.
