# 10 — Operating and backing up the lab

## Daily checks

```bash
# On Debian
systemctl is-active ssh
systemctl is-active docker
docker ps
docker stats --no-stream
free -h
df -h /
tailscale status
```

In Uptime Kuma, inspect monitor history and test that Telegram notifications still reach the intended chat periodically.

## Deployment and update hygiene

For each service, keep its Compose file in a dedicated directory and persist required application data separately from ephemeral containers.

```bash
cd ~/docker/<SERVICE>
docker compose ps
docker compose logs --tail 50
```

Before updates:

1. Read upstream release notes and check breaking changes.
2. Back up important application data, not just Compose YAML.
3. Pull a pinned, supported image version if appropriate.
4. Restart the service during a maintenance window.
5. Confirm healthy status and application functionality.

## Backup plan (NOT yet implemented)

A safe backup strategy should cover:

- **Proxmox/VM:** Guest configuration and an off-host VM backup.
- **Uptime Kuma:** Database/settings stored in its named Docker volume.
- **Jellyfin:** Configuration, metadata, and irreplaceable media files.
- **Recovery material:** Secrets and recovery codes, held in a protected location separate from this repository.

A *snapshot* alone is not an independent backup. Store copies outside the physical ThinkPad and periodically **test restores**. This repository does not claim these measures are already in place.

## Capacity planning

The host's RAM is limited (~8 GB) and the Debian guest currently has 4 GB. Avoid assuming enough resources for transcoding, large AI workloads, or numerous additional services. Track utilization under representative load and preserve filesystem free space.

## Power and connectivity

Because this is a single laptop host, any host power or connectivity outage can take down all VM services. Uptime Kuma inside that VM cannot reliably alert on a host-wide outage. Consider an external availability probe if outage notification during local failures becomes a requirement.
