# UniFi Controller

## Purpose
Management controller for the lab's UniFi access point (UniFi AP Pro per the audit form).

## Where it runs
- Type: VM on Proxmox (DL380).
- Host: `proxmox` (`192.168.20.10`).
- VM ID: **104**.
- Hostname: `unifi`.
- IP: `192.168.20.59` (VLAN 20).
- MAC: `bc:24:11:2d:82:1a`.
- Guest OS: Ubuntu Server.

## Configuration overview
- Adopted devices: TODO (UniFi AP Pro).
- SSIDs / VLAN mapping: TODO — VLAN tagging on the AP is not configured yet (audit form: "VLAN mapping: no").
- Controller backup schedule: TODO.

## Dependencies
- L2 reachability between controller and UniFi AP (broadcast / specific UDP).
- DNS: Pi-hole (`192.168.10.2`).

## Known issues / TODO
- TODO: declarative netplan config on `192.168.20.59`.
- TODO: design SSID-to-VLAN mapping once VLAN 40/50/60 are in place.
