# Immich

## Purpose
Self-hosted photo and video management service.

## Where it runs
- Type: VM on Proxmox (DL380).
- Host: `proxmox` (`192.168.20.10`).
- VM ID: **103**.
- Hostname: `immich`.
- IP: `192.168.20.53` (VLAN 20).
- MAC: `bc:24:11:c9:b5:18`.
- Guest OS: Ubuntu Server.

## Configuration overview
- Photo / video storage: TrueNAS (NFS path on R410, per audit form).
- Database: TODO (Postgres bundled with Immich or separate VM).
- TODO: capture which Immich version is running.

## Dependencies
- TrueNAS NFS share on Dell R410 (`192.168.20.11`). See storage troubleshooting runbook for the historical path mismatch.
- DNS: Pi-hole (`192.168.10.2`).

## Known issues / TODO
- Historical NFS path mismatch already documented — see [`docs/60-runbooks/immich_troubleshooting_solution.md`](../60-runbooks/immich_troubleshooting_solution.md) and the matching command explanations file.
- TODO: declarative netplan config on `192.168.20.53`.
- TODO: confirm backup story (snapshots on TrueNAS, retention).
