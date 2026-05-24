# Dell R410 — TrueNAS

## Purpose
Primary storage server running **TrueNAS SCALE**. Hosts datasets/shares used by the homelab
(notably Jellyfin and Nextcloud per the audit form).

## Physical / connectivity
- Connected switch port: **Port 3**
- Link speed: TODO (likely 1Gb unless upgraded)
- Notes: storage services for Proxmox nodes and clients

## Network
- Active VLANs in the homelab: **10 / 20 / 30** (`192.168.10.0/24`, `192.168.20.0/24`, `192.168.30.0/24`).
- TrueNAS IP: `192.168.20.11` (VLAN 20). Web UI requires `/ui/` path.
- MAC: `5c:ed:8c:1b:6e:6c`.
- Hostname: `truenas-r410`.
- iDRAC management IP: TODO (expected on VLAN 30).

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
