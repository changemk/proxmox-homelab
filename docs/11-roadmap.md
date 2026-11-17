# 11 — Project roadmap

## Completed

- [x] Proxmox installed on repurposed ThinkPad
- [x] Debian 13 VM provisioned; guest agent configured
- [x] Headless SSH administration working
- [x] Docker Engine and Compose installed and tested
- [x] Uptime Kuma monitoring with Telegram notification test
- [x] Tailscale installed on Proxmox and Debian; Android remote connectivity verified
- [x] Proxmox TOTP 2FA enabled
- [x] Jellyfin deployed, media libraries populated, Android access tested

## Planned work

### Near-term

- [ ] Automate encrypted off-host backups of the VM and service state.
- [ ] Check whether Jellyfin monitoring is configured. Add and test an Uptime Kuma check if needed.
- [ ] Track disk health, storage growth, and resource usage over time.
- [ ] Improve media storage organization and assess storage expansion.

### Intermediate

- [ ] Write Ansible playbooks for base configuration.
- [ ] Define a container update strategy with pre-update backups and a rollback procedure.
- [ ] Restore VM and service backups in a clean environment and document verification.
- [ ] Centralize logs and define retention and access controls.
- [ ] Test SSH key authentication with a recovery path, document least-privilege tailnet policies, and evaluate an independent availability monitor.

### Long-term

- [ ] Manage VM provisioning with infrastructure as code.
- [ ] Add CI validation and a controlled deployment workflow for configuration.
- [ ] Evaluate additional services such as Syncthing, Navidrome, or Immich against available resources.
- [ ] Write a disaster recovery runbook using the results of restore testing.
- [ ] Segment management and application traffic and verify the expected access paths.

## Portfolio milestones

1. Publish sanitized docs, diagrams and Compose examples.
2. Add verified configuration and UI screenshots without exposing secrets or personal content.
3. Demonstrate a reproducible service deployment from the documented configuration.
4. Implement off-host backups and show a **successful restore**; this would be a particularly valuable future resume bullet.
