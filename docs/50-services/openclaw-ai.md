# OpenClaw AI

## Purpose
OpenClaw AI is the homelab's AI component. The user has flagged this as the most painful
service in the current setup: it does not run as expected.

This page collects what we know and what still needs to be checked.

## Where it runs
- Type: VM (per `forms/homelab-audit-form.md`).
- Host: TODO (which Proxmox node — DL380?).
- IP: TODO.
- VLAN: TODO (likely VLAN 20 — Servers).
- Hostname: TODO.

## Resources
- vCPU: TODO
- RAM: TODO
- GPU passthrough (if any): TODO
- Disk(s): TODO

## File layout
- Install directory: TODO
- Soul / model files location: TODO
- Are soul files organized? TODO (audit form lists this as an open question).

## Known issues
- Service is "not running as expected" — symptom and reproduction steps to be captured here.
- TODO: collect exact error messages / logs.
- TODO: list dependencies (network, storage, DNS).

## TODO (next investigation steps)
1. Identify the host VM (Proxmox node + VMID).
2. Capture current IP and confirm reachability from VLAN 10 (management).
3. Read the latest service logs and paste relevant excerpts here.
4. Document the directory structure (install dir + data/model dirs).
5. Decide whether issues are config, resource, or networking related.
