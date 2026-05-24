# Proxmox VM Inventory

> Authoritative source: [`docs/00-overview/homelab-summary.md`](../00-overview/homelab-summary.md).
> All VMs below run on **HPE DL380 Gen9** (Proxmox host `192.168.20.10`, Proxmox VE 9.2.2).
> Guest OS: Ubuntu Server.

## VMs on `proxmox` (DL380)

| VM ID | Hostname | IP | Role | MAC | Netplan status | Service page |
|---:|---|---|---|---|---|---|
| 100 | nginx | `192.168.20.55` | Reverse proxy | `bc:24:11:af:dc:19` | ⏳ to configure | [`docs/50-services/nginx.md`](../50-services/nginx.md) |
| 101 | ollama | `192.168.20.57` | AI / LLM runtime | `bc:24:11:a8:b3:f9` | ⏳ to configure | [`docs/50-services/ollama.md`](../50-services/ollama.md) |
| 102 | grafana | `192.168.20.54` | Monitoring dashboards | `bc:24:11:d1:5a:82` | ⏳ to configure | [`docs/50-services/grafana.md`](../50-services/grafana.md) |
| 103 | immich | `192.168.20.53` | Photo / video management | `bc:24:11:c9:b5:18` | ⏳ to configure | [`docs/50-services/immich.md`](../50-services/immich.md) |
| 104 | unifi | `192.168.20.59` | UniFi controller | `bc:24:11:2d:82:1a` | ⏳ to configure | [`docs/50-services/unifi.md`](../50-services/unifi.md) |
| 105 | prometheus | `192.168.20.52` | Monitoring metrics | `bc:24:11:97:e9:2c` | ⏳ to configure | [`docs/50-services/prometheus.md`](../50-services/prometheus.md) |
| 106 | fitnessapp | `192.168.20.50` | Diet / fitness application | `bc:24:11:20:b1:eb` | ⏳ IP hard-coded | [`docs/50-services/fitnessapp.md`](../50-services/fitnessapp.md) |
| 107 | fitnessapp-db | `192.168.20.51` | Database for fitnessapp | `bc:24:11:04:31:6d` | ⏳ IP hard-coded | [`docs/50-services/fitnessapp.md`](../50-services/fitnessapp.md) |
| 108 | openclaw | `192.168.20.56` | OpenClaw AI | `bc:24:11:27:2d:72` | ⏳ to configure | [`docs/50-services/openclaw-ai.md`](../50-services/openclaw-ai.md) |
| 109 | wazuh | `192.168.20.58` | SIEM / security | `bc:24:11:92:4e:5b` | ⏳ to configure | [`docs/50-services/wazuh.md`](../50-services/wazuh.md) |

## Network notes
- All VMs sit on **VLAN 20** (`192.168.20.0/24`).
- Static lease assignments by MAC live in pfSense DHCP (pool `192.168.20.100`–`.200`, statics outside the pool).
- "Netplan status" indicates whether the guest already has its declarative static configuration applied or still relies on a hard-coded address / DHCP lease.

## Next steps
- Migrate every `⏳ to configure` / `⏳ IP hard-coded` VM to a declarative netplan static config matching the table above.
- Capture Proxmox bridge layout (`vmbr0`, VLAN-aware setting, tagged sub-interfaces) in a sibling page, `networking-bridges-vlan.md`.
- Decide cluster vs standalone for the DL380; record version + cluster state in `proxmox-cluster.md`.
- Document backup target — likely the R730 (planned PBS).
