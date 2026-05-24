# Virtualization

This section documents Proxmox configuration: cluster (or standalone nodes), bridges,
VLAN-aware networking, and the inventory of VMs / containers running on the cluster.

## Pages
- [`vm-inventory.md`](vm-inventory.md) — VMs with VMID, hostname, IP, role, MAC, netplan status.

## Planned pages
- `proxmox-cluster.md` — node list, versions, cluster vs standalone, quorum.
- `networking-bridges-vlan.md` — `vmbr*` bridges, VLAN-aware setting, mapping VM → VLAN.

## Related
- Host page: [`docs/10-hardware/servers/dl380-proxmox.md`](../10-hardware/servers/dl380-proxmox.md) — DL380 runs Proxmox VE 9.2.2 at `192.168.20.10`.
- Backup target (planned): [`docs/10-hardware/servers/r730-truenas.md`](../10-hardware/servers/r730-truenas.md) (potential PBS host — TBC).
- Note: Dell R320 is **not** a Proxmox node — see [`dell-r320.md`](../10-hardware/servers/dell-r320.md).
