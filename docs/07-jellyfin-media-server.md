# 07 — Jellyfin media server

## Purpose

Self-host a streaming interface for media the operator already owns, organized into movie and TV-show libraries. Jellyfin does **not** supply streaming-platform movie catalogs or subscriptions.

## Implemented result

- Deployed **Jellyfin** using Docker Compose on Debian.
- Container showed healthy status and exposed TCP **8096**.
- Added **Movies** and **Shows** libraries.
- Transferred media files from a separate computer.
- Scanned library metadata; shows and movies appeared.
- Used Jellyfin's Android app through Tailscale for remote access and playback.

No media content is included in this repository.

## Folder layout and Compose mount

```text
~/docker/jellyfin/
├── compose.yaml
├── config/      # persistent settings, excluded from Git
├── cache/       # cache, excluded from Git
└── media/       # personal content, excluded from Git
    ├── movies/
    └── shows/
```

The Compose file maps `./media` on Debian to `/media` inside the container. Jellyfin libraries therefore point to:

- Movies: `/media/movies`
- Shows: `/media/shows`

See [the sanitized Compose example](../docker/jellyfin/compose.yaml). Check the account's UID/GID with `id -u` and `id -g`; the example runs as `1000:1000`, which might differ on a new installation.

## Reproduction

```bash
mkdir -p ~/docker/jellyfin/{config,cache,media/movies,media/shows}
cd ~/docker/jellyfin
# Place the reviewed compose.yaml here and set HOMELAB_BIND_IP in a local .env
docker compose up -d
docker ps
```

Open `http://<DEBIAN_LAN_IP>:8096`, create a Jellyfin administrator account, define the libraries, and complete the wizard. Remote use was tested via `http://<DEBIAN_TAILSCALE_IP>:8096` with Tailscale active.

## Transferring content

For individual files (run from the laptop):

```bash
scp /path/to/owned-video.mkv <DEBIAN_USER>@<DEBIAN_LAN_IP>:~/docker/jellyfin/media/movies/
```

For a TV-series folder:

```bash
scp -r '/path/to/TV Shows/Example Series' \
  <DEBIAN_USER>@<DEBIAN_LAN_IP>:~/docker/jellyfin/media/shows/
```

For larger resumable transfers, install `rsync` on **both** sender and receiver and then use it. Example:

```bash
rsync -avh --partial --progress \
  '/path/to/Movies/' \
  <DEBIAN_USER>@<DEBIAN_LAN_IP>:~/docker/jellyfin/media/movies/
```

**Caution:** `--ignore-existing` skips partially transferred destination files. Avoid it for reliable resuming unless you understand the implications.

After file transfer, run **Dashboard → Scheduled Tasks → Scan Media Library** (UI wording may vary by release) and verify that the desired folders are included in library configuration.

## Resource considerations

With **4 GB VM RAM and 2 vCPUs**, prefer compatible client formats and **direct play** rather than CPU-heavy transcoding. Actual workload must be tested; this documentation makes no throughput claims.

Check resources with:

```bash
docker stats --no-stream jellyfin
df -h /
du -sh ~/docker/jellyfin/media/*
```

Maintain free storage and a separate copy of important media. Personal media, artwork, and private user settings should never be uploaded to GitHub.
