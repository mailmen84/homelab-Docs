# Ollama

## Purpose
Local LLM runtime for the lab (model serving for OpenClaw and other AI experiments).

## Where it runs
- Type: VM on Proxmox (DL380).
- Host: `proxmox` (`192.168.20.10`).
- VM ID: **101**.
- Hostname: `ollama`.
- IP: `192.168.20.57` (VLAN 20).
- MAC: `bc:24:11:a8:b3:f9`.
- Guest OS: Ubuntu Server.

## Configuration overview
- Models loaded: TODO.
- Default API port: `11434` (confirm exposed/firewalled correctly).
- GPU passthrough: TODO (Unknown — capture from Proxmox host config).

## Dependencies
- Consumed by [OpenClaw AI](openclaw-ai.md) (`192.168.20.56`).
- DNS: Pi-hole (`192.168.10.2`).

## Known issues / TODO
- TODO: declarative netplan config on `192.168.20.57`.
- TODO: model inventory.
- TODO: clarify whether OpenClaw's hangs correlate with Ollama load.
