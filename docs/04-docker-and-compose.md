# 04 — Docker Engine and Docker Compose

## Why Docker?

Containers package application runtime requirements while sharing the Linux kernel. Compose defines multi-container services in readable YAML. The Debian VM is the container host; Proxmox is not the container host in this setup.

## Installation used

The installation followed Docker's **official Debian repository**, not the convenience script. For version-specific instructions, see <https://docs.docker.com/engine/install/debian/>.

```bash
sudo apt update
sudo apt install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

sudo tee /etc/apt/sources.list.d/docker.sources >/dev/null <<EOF
Types: deb
URIs: https://download.docker.com/linux/debian
Suites: $(. /etc/os-release && echo "$VERSION_CODENAME")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

If pre-existing Docker packages conflict, consult upstream removal/migration instructions **before** modifying the system.

## What was verified

```bash
sudo systemctl is-active docker
docker --version
docker compose version
sudo docker run hello-world
```

The lab successfully displayed `Hello from Docker!`, confirming image download and container execution.

## Access control

To grant an administrative account direct access to Docker:

```bash
sudo usermod -aG docker <DEBIAN_USER>
# Log out and reconnect to refresh group membership
```

**Warning:** The Docker group grants root-equivalent control on the guest. This is a deliberate convenience/security tradeoff. `sudo docker ...` avoids granting that persistent group privilege.

## Common commands

```bash
docker ps                  # running containers
docker ps -a               # all containers
docker logs <name> --tail 50
docker stats --no-stream

docker compose up -d       # from a Compose project folder
docker compose ps
docker compose logs --tail 50
docker compose pull
docker compose up -d       # recreate with updated image after review
```

Do not automatically pull `latest` images without checking release notes and planning backups; a major version change may require a migration.

## Applications in this lab

- Uptime Kuma: [`../docker/uptime-kuma/compose.yaml`](../docker/uptime-kuma/compose.yaml)
- Jellyfin: [`../docker/jellyfin/compose.yaml`](../docker/jellyfin/compose.yaml)

These are sanitized templates. In each Compose directory, create a local, ignored `.env` file and set `HOMELAB_BIND_IP` to the Debian guest’s Tailscale address for private remote access, its trusted LAN address for LAN access, or `127.0.0.1` for access from the guest itself. Use the address you selected when opening the service. The templates require an explicit bind address.
