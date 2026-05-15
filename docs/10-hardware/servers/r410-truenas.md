# Dell R410 — TrueNAS

## Purpose
Primary storage server (TrueNAS). Hosts datasets/shares used by the homelab.

## Physical / connectivity
- Connected switch port: **Port 3**
- Link speed: TODO (likely 1Gb unless upgraded)
- Notes: storage services for Proxmox nodes and clients

## Network
- Active VLANs in the homelab: **10 / 20 / 30** (`192.168.10.0/24`, `192.168.20.0/24`, `192.168.30.0/24`).
- TrueNAS IP (per network-summary): `192.168.20.112` (VLAN 20). Web UI requires `/ui/` path.
- TrueNAS management IP: confirm and update once verified.
- Hostname: TODO

## Storage layout (to fill)
- Pool name(s): TODO
- VDEV layout (RAIDZ / mirror): TODO
- Disk list: TODO
- Datasets: TODO

## Shares / services (to fill)
- SMB shares: TODO
- NFS shares (if used): TODO
- Users / permissions model: TODO

## Backup / snapshots (to fill)
- Snapshot schedule: TODO
- Replication targets: TODO

## TODO (data to collect)
- TrueNAS version (SCALE/CORE + version)
- Pool and dataset structure
- Share paths and permissions
- Network settings (static IP, DNS, gateway)
