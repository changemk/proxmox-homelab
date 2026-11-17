# 09 — Troubleshooting log

The cases here reflect issues encountered during the build. The commands are designed to distinguish causes before changing configuration.

## 1. SSH connection timed out

**Symptom:** `ssh: connect to host <IP> port 22: Connection timed out`.

**Finding:** A previously used Debian LAN IP was stale; the VM's address later changed.

**Diagnosis:** In the Proxmox VM console:

```bash
hostname -I
systemctl is-active ssh
```

Use the **current guest IP**, then retry from the client. For a durable solution, consider a router DHCP reservation. Do not confuse the Proxmox host IP with the Debian VM IP.

## 2. Docker not running / group permissions

**Symptom:** Docker permissions error without `sudo`, or `docker ps` shows no containers.

**Checks:**

```bash
systemctl is-active docker
docker ps -a
groups
```

Adding a user to the Docker group requires a fresh session; this group gives root-equivalent access. An empty `docker ps` output only means no containers are currently running; it does not imply Docker is broken.

## 3. Telegram notification test HTTP 400

**Symptom:** Uptime Kuma's Telegram test returned `400 Bad Request`.

**Resolution observed:** After starting the bot conversation and correcting/obtaining the numeric Chat ID, the Telegram test succeeded.

**Checklist:** Ensure BotFather token is valid, the bot chat has been started, the Chat ID is numeric, and the message format is accepted. Never paste the token into public logs or support discussions.

## 4. Tailscale connected but DNS unavailable

**Symptom:** Android Tailscale displayed an unreachable DNS warning.

**Diagnostic:** Test direct access by **numeric tailnet IP** first; this isolates hostname resolution from tunnel reachability. Inspect tailnet DNS settings if the warning remains. The lab later successfully accessed Proxmox, Debian SSH, and dashboards remotely.

## 5. Jellyfin unreachable on old LAN address

**Symptom:** Browser couldn't connect to Jellyfin despite a healthy Docker container.

**Finding:** Debian's LAN address had changed.

**Checks:**

```bash
hostname -I
docker ps
curl -I http://localhost:8096
```

Use the current Debian LAN IP, or the Debian **Tailscale** IP while connected to the tailnet.

## 6. `rsync: command not found` on remote system

**Symptom:** Local rsync invocation failed with remote-side `rsync: command not found` and protocol error 12.

**Resolution:** Install `rsync` on the Debian **receiver** as well:

```bash
sudo apt update && sudo apt install -y rsync
```

Rerun the transfer from the sender. The `--partial` option can retain incomplete files, but combining it with `--ignore-existing` can prevent resumed transfers from updating those incomplete files.

## 7. Movies present on disk but absent in Jellyfin

**Symptom:** Movie files existed in the guest media directory but were not initially visible in Jellyfin.

**Checks:**

```bash
ls -lh ~/docker/jellyfin/media/movies/
docker exec jellyfin ls -lh /media/movies/
docker logs jellyfin --tail 100
```

Both the host files and the container-visible files were confirmed. The remaining checks concerned the `Movies` library's path and running the **Scan Media Library** scheduled task. The movies subsequently appeared in Jellyfin. Avoid claiming a specific root cause without definitive log evidence.

## 8. Storage and memory constraints

**Observation:** VM was originally ~2 GB RAM, later increased to 4 GB. A guest `df -h` observation showed a ~116 GB root filesystem with substantial free space *at that point in time*.

**Checks:**

```bash
free -h
nproc
df -h /
du -sh ~/docker/jellyfin/media
docker stats --no-stream
```

Monitor after media transfers. Values from earlier logs are historical and should not be interpreted as today's available capacity.
