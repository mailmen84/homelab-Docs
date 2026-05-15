# Attachments

Diagrams, exported (redacted) configs, and images supporting the documentation.

## Suggested layout
- `diagrams/` — `topology.drawio` + exported `topology.png`, VLAN diagrams.
- `configs-redacted/` — pfSense config XML with secrets stripped, switch config dumps.
- `screenshots/` — UI screenshots only when they help (no credentials visible).

## Rules
- **Never** commit configs that contain credentials or private keys.
- If exporting pfSense config, strip RADIUS/VPN secrets before committing.

> Status: scaffolding only.
