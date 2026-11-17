# Architecture diagram

```mermaid
flowchart TB
    Remote[Android phone / admin laptop]
    TS[(Tailscale private network)]
    Remote <--> TS

    subgraph ThinkPad[Lenovo ThinkPad T460s — Proxmox VE]
        Host[Proxmox Web UI :8006\nTailscale + TOTP]
        subgraph VM[Debian 13 VM — 4 GB RAM, 2 vCPUs]
            Debian[SSH :22 + Tailscale]
            Docker[Docker Engine and Compose]
            Kuma[Uptime Kuma :3001]
            Jellyfin[Jellyfin :8096]
            Data[(Persistent Docker data\nPrivate media folders)]
            Debian --> Docker
            Docker --> Kuma
            Docker --> Jellyfin
            Kuma --> Data
            Jellyfin --> Data
        end
        Host --> VM
    end

    TS <--> Host
    TS <--> Debian
    Kuma -- TCP port 8006 check --> Host
    Kuma -- TCP port 22 check --> Debian
    Kuma -- Telegram API notification --> Bot[Telegram]
```

## Trust boundaries

- The **Proxmox host** is the virtualization management plane.
- The **Debian guest** is the container/application plane.
- **Tailscale** provides private overlay connectivity to both nodes; it does not by itself make an application secure.
- **Docker published ports** are available on the guest's bound interfaces; do not assume they are automatically restricted to Tailscale.
- **Uptime Kuma** monitors selected service endpoints and depends on the same guest it monitors.

## Status

This represents deployed components reported during the project, not planned services such as Immich or off-host backups.
