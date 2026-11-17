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

## Planned — not implemented

- [ ] Automated encrypted **off-host backups** of VM and service state
- [ ] Full **restore drill** documented from a clean install
- [ ] **Ansible** playbooks for base system configuration
- [ ] Dashboards for resource metrics and longer-term capacity trends
- [ ] SSH key authentication with tested recovery path
- [ ] A documented tailnet least-privilege policy
- [ ] Optional Syncthing for files or Navidrome for music
- [ ] Optional Immich evaluation on constrained hardware with machine learning disabled
- [ ] Storage expansion and disk-health alerting
- [ ] A second external availability monitor for host/VM failures

## Portfolio milestones

1. Publish sanitized docs, diagrams and Compose examples.
2. Add verified configuration and UI screenshots without exposing secrets or personal content.
3. Demonstrate a reproducible service deployment from the documented configuration.
4. Implement off-host backups and show a **successful restore**; this would be a particularly valuable future resume bullet.
