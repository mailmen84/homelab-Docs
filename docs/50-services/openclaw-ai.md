# OpenClaw AI

## Purpose
OpenClaw AI is the homelab's AI assistant. Originally intended to help maintain the homelab repo,
read email, and read the calendar. The user has flagged this as the most painful service in the
current setup: it does not run reliably.

## Where it runs
- Type: VM on Proxmox (DL380).
- Host: `proxmox` (`192.168.20.10`).
- VM ID: **108**.
- Hostname: `openclaw`.
- IP: `192.168.20.56` (VLAN 20).
- MAC: `bc:24:11:27:2d:72`.
- Guest OS: Ubuntu Server.

## Configuration overview
- Install directory: TODO
- "Soul" / model files location: TODO (audit form says these are organized)
- Resource allocation (vCPU / RAM / GPU passthrough): TODO

## Dependencies
- Network: VLAN 20 connectivity, outbound internet for API calls.
- DNS: Pi-hole (`192.168.10.2`) via pfSense.
- Storage: TODO (likely TrueNAS dataset on R410/R730).
- External APIs: Google account (mail/calendar), other API tokens — TODO.

## Known issues
- Frequently "hangs" mid-task: claims a task is finished, then asks for the API token or password again a few minutes later.
- Google account authorization is unstable: works for ~1 day, then re-authorization is required.
- Likely root causes (to investigate): token/credential storage and lifecycle, session persistence, network path to upstream APIs.

## TODO (next investigation steps)
1. Capture exact error messages / logs from VM 108 around the credential re-prompt issue.
2. Confirm OpenClaw network reachability from VLAN 10 (management) and to the internet.
3. Document the credential/token store and renewal flow.
4. Document the install directory and "soul" / model directory layout.
5. Decide whether the issue is configuration, resource, or external-dependency related.
