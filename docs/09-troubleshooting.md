# 09 — Troubleshooting log

The cases here reflect issues encountered during the build. The commands are designed to distinguish causes before changing configuration.

## 1. SSH connection timed out

**Problem** `ssh: connect to host <IP> port 22: Connection timed out`.

**Diagnosis** A previously used Debian LAN IP was stale; the VM's address later changed.

**Diagnostic commands** In the Proxmox VM console:

```bash
hostname -I
systemctl is-active ssh
```

**Resolution** Retrieved the current Debian guest IP in the VM console and reconnected from the client.

**Lesson learned** Verify the guest address before changing SSH settings. A router DHCP reservation can keep that address predictable.

## 2. Docker not running / group permissions

**Problem** Docker initially required `sudo` for the administrative account.

**Diagnosis** The account lacked access to the Docker socket. Check service status and group membership to distinguish that from a stopped daemon.

**Diagnostic commands**

```bash
systemctl is-active docker
docker ps -a
groups
```

**Resolution** Added the trusted administrative user to the Docker group and started a fresh login session to refresh membership.

**Lesson learned** Docker group membership grants root-equivalent access. An empty `docker ps` listing indicates there are no running containers. Use `docker ps -a` to include stopped containers.

## 3. Telegram notification test HTTP 400

**Problem** Uptime Kuma's Telegram test returned `400 Bad Request`.

**Diagnosis** Telegram rejected the notification request. The exact configuration fault was unconfirmed.

**Resolution** Checked chat initialization and the numeric chat ID, corrected the notification configuration, and confirmed test delivery.

**Lesson learned** Verify delivery end to end. Ensure BotFather token is valid, the bot chat has been started, the Chat ID is numeric, and the message format is accepted. Never paste the token into public logs or support discussions.

## Private connectivity from outside the LAN

**Problem** Needed to reach Proxmox and Debian while away from home.

**Diagnosis** The management endpoints needed a private network path from outside the home LAN.

**Resolution** Installed Tailscale on the Proxmox host, Debian VM, and Android phone. With Wi-Fi off and mobile data active, verified access to the Proxmox UI and Debian SSH. See [the remote access guide](06-tailscale-remote-access.md).

**Lesson learned** Test from a separate network and verify each intended service independently.

## 4. Tailscale connected but DNS unavailable

**Problem** Android Tailscale displayed an unreachable DNS warning.

**Diagnostic:** Test direct access by **numeric tailnet IP** first; this isolates hostname resolution from tunnel reachability. Inspect tailnet DNS settings if the warning remains. The lab later successfully accessed Proxmox, Debian SSH, and dashboards remotely.

## 5. Jellyfin unreachable on old LAN address

**Problem** Browser couldn't connect to Jellyfin despite a healthy Docker container.

**Diagnosis** Debian's LAN address had changed.

**Diagnostic commands**

```bash
hostname -I
docker ps
curl -I "http://${HOMELAB_BIND_IP}:8096"
```

Set `HOMELAB_BIND_IP` in the shell to the actual service bind address for the curl check. Use the current Debian LAN IP, or the Debian **Tailscale** IP while connected to the tailnet.

## 6. `rsync: command not found` on remote system

**Problem** Local rsync invocation failed with remote-side `rsync: command not found` and protocol error 12.

**Diagnosis** The Debian receiver did not have `rsync` installed.

**Resolution** Installed `rsync` on Debian and retried the transfer from the sender.

```bash
sudo apt update && sudo apt install -y rsync
```

**Lesson learned** Remote-shell transfers require `rsync` at both ends. The `--partial` option can retain incomplete files, but combining it with `--ignore-existing` can prevent resumed transfers from updating those incomplete files.

## 7. Movies present on disk but absent in Jellyfin

**Problem** Movie files existed in the guest media directory but were not initially visible in Jellyfin.

**Diagnostic commands**

```bash
ls -lh ~/docker/jellyfin/media/movies/
docker exec jellyfin ls -lh /media/movies/
docker logs jellyfin --tail 100
```

**Diagnosis** The files existed on the host. Mounts, in-container visibility, and the library path were the next checks.

**Resolution** Verified the volume mounts and container-visible files, checked the Movies library path, and rescanned with **Scan Media Library**. The movies subsequently appeared.

**Lesson learned** Verify the path from host storage through the mount to the library index. The exact cause was unconfirmed.

## 8. Storage and memory constraints

**Observation:** VM was originally ~2 GB RAM, later increased to 4 GB. A guest `df -h` observation showed a ~116 GB root filesystem with substantial free space *at that point in time*.

**Diagnostic commands**

```bash
free -h
nproc
df -h /
du -sh ~/docker/jellyfin/media
docker stats --no-stream
```

Monitor after media transfers. Values from earlier logs are historical and should not be interpreted as today's available capacity.
