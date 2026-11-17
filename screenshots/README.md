# Portfolio screenshot checklist

Add screenshots **only after sanitizing them**. There are no private screenshots included by default.

Suggested evidence:

1. Proxmox node overview, cropping out addresses and unique device IDs.
2. Debian VM hardware allocation showing 4 GB RAM and 2 vCPUs.
3. `docker ps` showing healthy Uptime Kuma and Jellyfin containers (review output first).
4. Uptime Kuma monitor overview with private names, IPs, emails, and tokens removed.
5. Tailscale device connectivity overview with private tailnet details masked.
6. Jellyfin's generic empty or demo library view — **do not publish copyrighted personal video files or identifiable library content without clearance**.

## Safety checks

- Crop or redact personal emails, public/private IPs, Tailscale addresses, user IDs and device identifiers.
- Never show Telegram bot tokens, chat IDs, password prompts or recovery codes.
- Screenshots can embed metadata; strip EXIF before publishing.
- Use sample or synthetic data if a screenshot risks revealing private details.
- Public screenshots should be clearly described as actual evidence or representative illustrations.

`.gitignore` excludes common image extensions by default. If you intentionally add a reviewed image, use `git add -f screenshots/<SAFE_IMAGE_NAME>.png` after verifying it contains no private details.
