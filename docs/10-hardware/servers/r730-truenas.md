# Dell R730 — TrueNAS

## Purpose
Secondary storage host running **TrueNAS SCALE**.
Planned role: **Proxmox Backup Server (PBS)** target — to be confirmed.

## Physical / connectivity
- Connected switch port: TODO
- Link speed: TODO
- Notes: Buffalo BS-MP2012 port not yet documented.

## Network
- Active VLANs in the homelab: **10 / 20 / 30** (`192.168.10.0/24`, `192.168.20.0/24`, `192.168.30.0/24`).
- TrueNAS management IP: `192.168.20.12/24` (VLAN 20), interface `enp6s0f1`.
- IPv6 on `enp6s0f1`: `fe80::20a:f7ff:fe86:6d85/64`.
- Default route (IPv4): `192.168.20.1`.
- DNS primary: `192.168.20.1`.
- Hostname in UI: `truenas` (documentation alias remains `truenas-r730`).
- Domain: `local`.
- Interface MACs:
  - `eno1`: `20:47:47:88:2a:54`
  - `eno2`: `20:47:47:88:2a:55`
  - `eno3`: `20:47:47:88:2a:56`
  - `eno4`: `20:47:47:88:2a:57`
  - `enp6s0f0`: `00:0a:f7:86:6d:84`
  - `enp6s0f1`: `00:0a:f7:86:6d:85`
- Service announcement: `NETBIOS-NS, mDNS, WS-DISCOVERY`.
- iDRAC/IPMI: `Channel 1` visible, IP address still TODO.

## Hardware summary
- Platform: `Generic`
- Edition: `Community`
- Version: `25.10.2 - Goldeye`
- Uptime (capture time): `27 days 9 hours 49 minutes` (as of 15:16)
- CPU: `Intel(R) Xeon(R) CPU E5-2609 v3 @ 1.90GHz`
- Memory: `157.3 GiB` ECC total

## Storage layout
- Primary pool: `tank` (status: `ONLINE`)
- Pool usage: `0.3%` used (`14.17 GiB`), available `4.2 TiB`, usable capacity `4.22 TiB`
- Total disks in pool view: `7`
- Disk errors: `0 of 7`
- Data VDEVs: `1 x RAIDZ1 | 5 wide | 1.09 TiB`
- Log VDEVs: `1 x MIRROR | 2 wide | 279.4 GiB`
- Data VDEV member disks (all ONLINE, no ZFS errors): `sda`, `sdd`, `sde`, `sdf`, `sdg` (`1.09 TiB` each)
- Log mirror member disks (all ONLINE, no ZFS errors): `sdb`, `sdc` (`279.4 GiB` each)
- Scrub schedule: `At 00:00, only on Sunday`
- Last scrub: `Finished Scrub on 2026-05-17 08:01:02`
- Last scan errors: `0`
- Last scan duration: `1 minute`
- Auto TRIM: `Off`
- Disk temperatures: highest `48°C`, lowest `37°C`, average `40.485°C`

## Datasets / shares / services
- Dataset tree visible:
  - `tank`
  - `tank/Proxmox-Dell`
- Dataset usage:
  - `tank`: `14.17 GiB / 4.2 TiB`
  - `Proxmox-Dell`: `11.95 GiB / 4.2 TiB`
- Encryption: `Unencrypted` (for visible datasets)
- NFS shares: `RUNNING`
  - enabled share: `/mnt/tank/Proxmox-Dell` (description: `Proxmox-Dell`)
- SMB shares service: `RUNNING` (no specific share entries visible on screenshot)
- iSCSI shares/targets service: `RUNNING`
- NVMe-oF subsystems: `STOPPED`

## Role TODO
- Confirm whether this host stays as TrueNAS SCALE long-term or is repurposed for PBS.
- If PBS: document the Proxmox node it backs up, retention policy, and verification job schedule.
- Add controller/HBA model and full disk model/serial details from the `Disks` view (not visible in the current screenshots).
- Add iDRAC/IPMI address.
