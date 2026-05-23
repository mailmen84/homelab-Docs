# Wazuh

## Purpose
SIEM / host-based security monitoring across the homelab (server logs, intrusion detection).

## Where it runs
- Type: VM on Proxmox (DL380).
- Host: `proxmox` (`192.168.20.10`).
- VM ID: **109**.
- Hostname: `wazuh`.
- IP: `192.168.20.58` (VLAN 20).
- MAC: `bc:24:11:92:4e:5b`.
- Guest OS: Ubuntu Server.

## Configuration overview
- Agents installed on: TODO (DL380, R410, R730, PC-TrueNAS, Pi-hole, …).
- Dashboard / UI port: TODO.
- Indexer storage: TODO.

## Dependencies
- DNS: Pi-hole (`192.168.10.2`).
- Outbound: rule database updates.

## Known issues / TODO
- Status: per audit form, the user "wants to install Wazuh for server logs" — confirm if the VM is already provisioned or still pending.
- TODO: declarative netplan config on `192.168.20.58`.
- TODO: list which hosts have the Wazuh agent installed.
