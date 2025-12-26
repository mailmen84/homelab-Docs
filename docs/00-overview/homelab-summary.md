# Homelab Summary

## Purpose
This documentation describes my homelab so it can be maintained and rebuilt reliably.
Current focus: documenting the existing state (temporary subnet) and planned segmentation (VLANs).

## Current devices (high-level)
- **DL380** — Proxmox (main compute; 10Gb uplink to switch on port 5)
- **R320** — Proxmox
- **R410** — TrueNAS (primary storage)
- **Buffalo BS-MP2012** — switch (core connectivity)
- **Raspberry Pi** — planned Prometheus/Grafana node
- **Buffalo TS-XL NAS** — secondary NAS
- **PCs** — user endpoints / management access

## Physical topology (current)
Switch port map (Buffalo BS-MP2012):
- Port 1: ISP / internet uplink
- Port 2: R320 (Proxmox)
- Port 3: R410 (TrueNAS)
- Port 4: PC
- Port 5: DL380 (Proxmox, 10Gb, trunk planned)
- Port 6: Buffalo NAS (TS-XL)

## Network status (current)
- Current subnet (temporary): **192.168.8.0/24**
- VLAN plan (target): **10 / 20 / 30 / 40 / 50 / 60**
- Trunk plan: **port 5** (DL380 uplink/trunk)
- Access ports: as previously defined (to be documented explicitly in the Network chapter)

## Next documentation tasks
- Define VLAN names/purposes and map them to switch ports (explicit table).
- Document Proxmox networking (bridges, VLAN awareness, management IPs).
- Document TrueNAS shares and dataset structure.
- Add a “rebuild-from-zero” runbook as the system stabilizes.
