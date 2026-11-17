# 05 — Uptime Kuma monitoring and Telegram alerting

## Purpose

Get visibility into whether specific services are reachable and receive notifications about availability changes.

## Deployment

Uptime Kuma v2 runs in Docker with port **3001** and a persistent named volume. An example is available at [`docker/uptime-kuma/compose.yaml`](../docker/uptime-kuma/compose.yaml).

```bash
mkdir -p ~/docker/uptime-kuma
cd ~/docker/uptime-kuma
# Place the reviewed compose.yaml here and set HOMELAB_BIND_IP in a local .env
docker compose up -d
docker compose ps
```

Access on your trusted LAN at `http://<DEBIAN_LAN_IP>:3001` or via Tailscale at `http://<DEBIAN_TAILSCALE_IP>:3001`. The initial setup used SQLite and created an administrator account.

## Monitors actually configured

| Monitor | Protocol | Target | What it proves |
| --- | --- | --- | --- |
| Proxmox Server | TCP Port | `<PROXMOX_LAN_IP>:8006` | Management TCP port is reachable |
| Debian SSH Server | TCP Port | `<DEBIAN_LAN_IP>:22` | SSH TCP port is reachable |

A green TCP port status **does not** confirm every aspect of the underlying server or application is healthy.

## Telegram notifications

1. Open Telegram and create a bot through the verified **@BotFather** using `/newbot`.
2. Start a private chat with your new bot and send `/start`.
3. In Uptime Kuma, open **Settings → Notifications** and configure the Telegram provider.
4. Enter your **bot token locally**; use **Auto Get** for the numeric chat ID if available.
5. Send a test message; confirm receipt on the phone.
6. Save the notification and enable it on desired monitors.

**Incident from this build:** An initial Telegram test returned **HTTP 400 Bad Request**; delivery succeeded after checking chat initialization and the chat ID and correcting the notification configuration. The exact configuration fault was unconfirmed. See [troubleshooting](09-troubleshooting.md).

Never publish the bot token or a screenshot containing it. If leaked, rotate it through BotFather.

## Operational limitation

Uptime Kuma is hosted **inside the Debian VM** it monitors. If Debian or the physical host loses power or connectivity, Uptime Kuma also becomes unavailable and cannot reliably send an outage alert itself. External monitoring is a future improvement.

## Other checks

To add Jellyfin monitoring, a possible HTTP URL is `http://<DEBIAN_LAN_IP>:8096/health`. Confirm its actual response and monitor behavior first; this repository does not claim this additional monitor was completed.
