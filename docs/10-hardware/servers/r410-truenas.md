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
- TrueNAS management IP: `192.168.20.11/24` (VLAN 20), interface `enp3s0f1`. Web UI path: `/ui/`.
- Default route (IPv4): `192.168.20.1`.
- DNS primary: `192.168.20.1`.
- Hostname configured in TrueNAS UI: `truenas` (documentation host label: `Dell R410-truenas`).
- Domain: `local`.
- Interface MACs:
  - `eno1`: `84:2b:2b:60:ee:27`
  - `eno2`: `84:2b:2b:60:ee:28`
  - `enp3s0f0`: `5c:ed:8c:1b:6e:68`
  - `enp3s0f1`: `5c:ed:8c:1b:6e:6c`
- Service announcement: `NETBIOS-NS, mDNS, WS-DISCOVERY`.
- iDRAC/IPMI: `Channel 1` visible; IP address still needs to be added.

## System facts (from screenshots)
- Platform: `Generic`
- Edition: `Community`
- Version: `25.10.2.1 - Goldeye`
- Uptime (capture time): `31 days 5 hours 47 minutes`
- CPU: `Intel(R) Xeon(R) CPU X5675 @ 3.07GHz`
- RAM: `125.9 GiB` ECC total available

## Storage layout
- Pool name: `tank`
- Pool status: `ONLINE`
- Used space: `63.58%` (`3.28 TiB`)
- Free/available: `1.88 TiB`
- Usable capacity: `5.15 TiB`
- VDEV layout: `1 x RAIDZ1 | 4 wide | 1.82 TiB disks`
- Data VDEVs: `1`
- Total disks in pool: `4`
- Disk errors: `0 of 4`
- Listed disks: `sda`, `sdb`, `sdc`, `sde` (all ONLINE, no errors)
- Serial numbers (from disk list):
  - `sda`: `WD-WCC4M6PVLK1X` (1.82 TiB, pool `tank`)
  - `sdb`: `WD-WCC4M6PVL79X` (1.82 TiB, pool `tank`)
  - `sdc`: `WD-WCC4M5HZHUTF` (1.82 TiB, pool `tank`)
  - `sde`: `WD-WCC4M6SULE5V` (1.82 TiB, pool `tank`)
  - `sdd`: `181749802330` (931.51 GiB, pool `boot-pool`)
  - `sdf`: `0701268F2B654F58` (14.44 GiB, unassigned)
- Unassigned disks: `1`

## Datasets
Pool `tank` includes at least:
- `apps`
- `home_przemek`
- `immich`
- `iso-library`
- `Kali`
- `media`
- `photos`
- `proxmox`
- `VM_disks`

## Shares / services
- SMB service: `RUNNING`
- SMB shares (enabled):
  - `english_movies` -> `/mnt/tank/media/English_movies`
  - `home_przemek` -> `/mnt/tank/home_przemek`
  - `immich-external` -> `/mnt/tank/photos/Immich-external`
  - `iso-library` -> `/mnt/tank/iso-library`
  - `movies` -> `/mnt/tank/media/movies`
  - `series` -> `/mnt/tank/media/Series`
- NFS service: `RUNNING`
- NFS shares (enabled):
  - `/mnt/tank/immich/library`
  - `/mnt/tank/iso-library`
  - `/mnt/tank/proxmox`
- iSCSI targets: service shows `RUNNING`, visible target name `proxmox`
- NVMe-oF subsystem: `STOPPED`

## Apps (Kubernetes/Apps)
- Apps service: `RUNNING`
- Installed apps (running): `jellyfin`, `nextcloud`
- jellyfin details visible:
  - App version: `10.11.8`
  - Container image version: `1.3.9`
  - Train: `community`
  - Port mapping visible: `30013 -> 8096` (IPv4 and IPv6)

## Backup / snapshots
- Snapshot count (root tank view): `0`
- Snapshot tasks: `0`
- Backup tasks: `No Backup Tasks`
- Scheduled scrub: `At 00:00, only on Sunday`
- Last scan: `Finished Scrub on 2026-05-03 01:34:18`
- Last scan errors: `0`
- Auto TRIM: `Off`

## TODO (remaining data to collect)
- Add controller/HBA model (a dedicated controller screen was not included in the current screenshots).
- Add full disk vendor/model names (currently we have device names, capacities, and serials).
- Add iDRAC/IPMI address (only `Channel 1` is visible on the screenshot).
- Confirm whether having no snapshot/backup tasks is the intended final state.
