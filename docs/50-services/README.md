# Services

This section documents application-level services running in the homelab.

> Authoritative VM-to-service mapping: [`docs/40-virtualization/vm-inventory.md`](../40-virtualization/vm-inventory.md).

## Pages
- [`openclaw-ai.md`](openclaw-ai.md) — OpenClaw AI (VM 108, `192.168.20.56`). The lab's AI assistant; currently flaky.
- [`grafana.md`](grafana.md) — Grafana dashboards (VM 102, `192.168.20.54`).
- [`prometheus.md`](prometheus.md) — Prometheus metrics (VM 105, `192.168.20.52`).
- [`immich.md`](immich.md) — Immich photo/video service (VM 103, `192.168.20.53`).
- [`nginx.md`](nginx.md) — Reverse proxy (VM 100, `192.168.20.55`).
- [`ollama.md`](ollama.md) — Ollama local LLM runtime (VM 101, `192.168.20.57`).
- [`wazuh.md`](wazuh.md) — Wazuh SIEM (VM 109, `192.168.20.58`).
- [`unifi.md`](unifi.md) — UniFi controller (VM 104, `192.168.20.59`).
- [`fitnessapp.md`](fitnessapp.md) — Diet/fitness app + DB (VMs 106 / 107).

## Network-level services (documented elsewhere)
- Pi-hole — [`../20-network/pi-hole.md`](../20-network/pi-hole.md) (Raspberry Pi at `192.168.10.2`).
- pfSense — [`../20-network/pfsense.md`](../20-network/pfsense.md).
- Tailscale subnet router — Dell Optiplex at `192.168.10.130` (TODO: dedicated page).

## TODO
- Promote Tailscale subnet router to its own page under `docs/20-network/`.
- Backfill TODOs in every per-service page (versions, ports, dependencies, backup story).
