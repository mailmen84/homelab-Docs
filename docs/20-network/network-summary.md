# Network Summary (Current + Planned)

## Current state (active VLANs)
- Active subnets:
  - **192.168.10.0/24** (VLAN 10)
  - **192.168.20.0/24** (VLAN 20)
  - **192.168.30.0/24** (VLAN 30)
- Notes:
  - VLAN segmentation is active and enforced by pfSense.
  - Tailscale subnet router (Ubuntu/NUC `192.168.10.130`) advertises these routes.

## VLAN Segmentation Overview
Active VLAN IDs and their corresponding IP subnets:
- **VLAN 10 (Management / LAN):** `192.168.10.0/24`
- **VLAN 20 (Servers):** `192.168.20.0/24`
- **VLAN 30 (Management / iLO / Other):** `192.168.30.0/24`
- **Planned additional VLANs:** 40, 50, 60 (purposes to be defined)

> VLAN names/purposes are partially defined above. A dedicated page (e.g., `vlan-plan.md`) will contain full details.

## Switch: Buffalo BS-MP2012 (port map)
Current cabling / port usage:
- Port 1: ISP (internet uplink)
- Port 2: R320 (Proxmox)
- Port 3: R410 (TrueNAS)
- Port 4: PC
- Port 5: DL380 (Proxmox, **10Gb**, planned **trunk**)
- Port 6: Buffalo TS-XL NAS

## Trunk vs access (planned)
- Port 5: **Trunk** (VLANs 10/20/30/40/50/60) to DL380
- Other ports: **Access** ports (as previously defined)
  - TODO: write the explicit “port → VLAN” access table once confirmed


## Active Service IP Addresses

Based on the current Tailscale documentation and nmap scans, here are the identified services and their IP addresses:

### VLAN 10 - Management / LAN

| Service | IP Address | URL / Access | Status | Notes |
|---|---:|---|---|---|
| Gateway / pfSense | `192.168.10.1` | `https://192.168.10.1:8443` | Needs confirmation | Gateway detected with DNS and nginx on port 8443 |
| Pi-hole | `192.168.10.2` | `http://192.168.10.2/admin` | Needs confirmation | Raspberry Pi / Pi-hole detected |
| Buffalo Web UI | `192.168.10.101` | `http://192.168.10.101` | Needs confirmation | Buffalo web interface detected |
| Cisco Switch | `192.168.10.200` | `ssh 192.168.10.200` / `http://192.168.10.200` | Needs confirmation | Cisco switch management detected |
| Tailscale Router | `192.168.10.130` | `ssh 192.168.10.130` | Working | Ubuntu / Intel NUC subnet router |

### VLAN 20 - Servers

| Service | IP Address | URL / Access | Status | Notes |
|---|---:|---|---|---|
| Gateway / pfSense VLAN 20 | `192.168.20.1` | `https://192.168.20.1:8443` | Needs confirmation | Gateway detected with DNS and nginx on port 8443 |
| Proxmox | `192.168.20.100` | `https://192.168.20.100:8006` | Working | Proxmox GUI uses HTTPS and port 8006 |
| Server / NAS candidate | `192.168.20.102` | `http://192.168.20.102` | Needs confirmation | SMB/NFS ports detected: 139, 445, 2049 |
| Tomcat service host | `192.168.20.103` | `http://192.168.20.103:8080` / `https://192.168.20.103:8443` | Needs confirmation | Apache Tomcat detected |
| Linux host | `192.168.20.104` | `ssh 192.168.20.104` | Needs confirmation | SSH/RPC detected |
| Monitoring candidate | `192.168.20.105` | `http://192.168.20.105:9090` | Needs confirmation | Go net/http server detected; possible monitoring service |
| Grafana | `192.168.20.108` | `http://192.168.20.108:3000` | Needs confirmation | Grafana detected by nmap |
| TrueNAS | `192.168.20.112` | `http://192.168.20.112/ui/` | Working | TrueNAS GUI requires `/ui/` path |
| Linux / Web host | `192.168.20.113` | `http://192.168.20.113:3000` | Needs confirmation | nginx and port 3389 detected |

### VLAN 30 - Management / iLO / Other

| Service | IP Address | URL / Access | Status | Notes |
|---|---:|---|---|---|
| Gateway / pfSense VLAN 30 | `192.168.30.1` | `https://192.168.30.1:8443` | Needs confirmation | Gateway detected with DNS and nginx on port 8443 |
| Linux host | `192.168.30.103` | `ssh 192.168.30.103` | Needs confirmation | Dropbear SSH detected |
| HP iLO | `192.168.30.105` | `https://192.168.30.105` | Needs confirmation | HP Integrated Lights-Out web interface detected |
| Unknown device | `192.168.30.108` | Unknown | Needs checking | Ports 53 and 8081 detected |

## Next steps for documentation
- Define VLAN names/purposes (e.g., in a dedicated `vlan-plan.md` page).
- Document switch port mapping: explicit “port → VLAN” access table.
- Document Proxmox networking (bridges, VLAN awareness, management IPs).
- Document TrueNAS shares and dataset structure.
- Create a “rebuild-from-zero” runbook as the system stabilizes.
- Add documentation for common homelab service ports and access methods.
- Consolidate confirmed working URLs and their specific access requirements.
- Identify and rename unknown devices.
- Add this `homelab-tailscale-service-map.md` file into the main `homelab-Docs` repository.


