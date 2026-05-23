# FitnessApp (diet / fitness application)

## Purpose
Personal diet / fitness application running in the homelab.
Two VMs are involved: the application server and its database.

## Where it runs
| Component | VM ID | Hostname | IP | MAC |
|---|---:|---|---|---|
| App | 106 | `fitnessapp` | `192.168.20.50` | `bc:24:11:20:b1:eb` |
| DB | 107 | `fitnessapp-db` | `192.168.20.51` | `bc:24:11:04:31:6d` |

- Host: `proxmox` (HPE DL380, `192.168.20.10`).
- VLAN: 20.
- Guest OS: Ubuntu Server.

## Configuration overview
- App-to-DB connectivity: TODO (port, credentials store).
- External access: presumably via the nginx reverse proxy at `192.168.20.55` — confirm.
- TLS / hostname: TODO.

## Dependencies
- DNS: Pi-hole (`192.168.10.2`).
- Storage: TODO (DB persistence — local disk vs TrueNAS NFS).

## Known issues / TODO
- Both VMs currently have **hard-coded** IPs in the guest (per `homelab-summary.md`); convert to declarative netplan with static lease parity.
- TODO: capture the application stack (framework, container vs systemd, etc.).
- TODO: define backup / dump schedule for the database.
