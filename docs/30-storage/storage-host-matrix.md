# Storage Host Matrix

Short operational overview of storage hosts without requiring physical access to the rack.

## Goal
- Keep one place with the current storage host status.
- Separate what is already confirmed vs what still needs remote verification.

## Storage hosts

| Host | Role | IP | VLAN | MAC | Physical port | Status |
|---|---|---:|---:|---|---|---|
| Dell R410-truenas (`truenas`) | Primary TrueNAS SCALE | `192.168.20.11` | 20 | `5c:ed:8c:1b:6e:6c` | 3 | active (doc refreshed from screenshots: TrueNAS 25.10.2.1, pool `tank` ONLINE) |
| Dell R730 (`truenas-r730`) | Secondary TrueNAS / planned PBS (TBC) | `192.168.20.12` | 20 | `00:0a:f7:86:6d:85` | TBD | active (doc refreshed from screenshots: TrueNAS 25.10.2 Goldeye, pool `tank` ONLINE, RAIDZ1 + LOG mirror) |
| PC Workstation (`truenas-pc`) | Additional TrueNAS host | `192.168.20.13` | 20 | `04:d9:f5:0f:43:ca` | TBD | active |

## What can be updated remotely (no physical access)

1. TrueNAS version on each host.
2. Pool list and health state.
3. Datasets and key SMB/NFS shares.
4. Snapshot tasks and replication (if configured).
5. R730 role confirmation (TrueNAS-only vs PBS target).

## What still needs physical verification later

1. Exact switch ports for R730 and `truenas-pc`.
2. Real link speed (1Gb/10Gb) for each host.
3. Cable labels and patch-panel mapping (if present).

## Next documentation step
After collecting the remote data:
- update `docs/10-hardware/servers/r410-truenas.md`
- update `docs/10-hardware/servers/r730-truenas.md`
- update `docs/10-hardware/servers/pc-truenas.md`
- refine `docs/30-storage/README.md` and the backup runbook.
