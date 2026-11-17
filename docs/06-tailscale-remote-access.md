# 06 — Remote access through Tailscale

## Requirement

Access the Proxmox management UI, Debian SSH, and Docker-hosted dashboards from outside the home LAN without opening inbound router ports.

## Implemented layout

Tailscale was installed **separately** on:

1. Proxmox VE host — to reach `https://<PROXMOX_TAILSCALE_IP>:8006`.
2. Debian guest — to reach `ssh <DEBIAN_USER>@<DEBIAN_TAILSCALE_IP>` and Docker services.
3. Android phone — to access these resources using mobile data.

The phone and servers joined the same private tailnet. Access to the Proxmox web interface, Debian SSH (via an Android terminal), Uptime Kuma, and later Jellyfin was tested successfully.

## Example installation

Run the following on the **intended node** (Proxmox Shell for Proxmox, Debian SSH for Debian):

```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
tailscale ip -4
tailscale status
```

Review scripts before executing them on a hypervisor. Tailscale may also provide distribution-specific repository installation methods: <https://tailscale.com/kb/>.

Authenticate using the URL shown by `tailscale up`, connect the Android app to the **same tailnet**, then test with Wi-Fi off and cellular data on.

## Access examples

```text
Proxmox:   https://<PROXMOX_TAILSCALE_IP>:8006
Uptime Kuma: http://<DEBIAN_TAILSCALE_IP>:3001
Jellyfin:  http://<DEBIAN_TAILSCALE_IP>:8096
SSH:       ssh <DEBIAN_USER>@<DEBIAN_TAILSCALE_IP>
```

**Important:** Installing Tailscale on the Proxmox host does **not automatically** expose a separate VM through the host. This project installed Tailscale in both places instead of configuring a subnet router.

## DNS warning encountered

The Android client showed a warning that its configured DNS servers were unreachable. The actual reachability test used a numeric Tailscale IP, which does not require hostname lookup. Check tailnet DNS configuration independently if the warning persists; avoid changing DNS servers blindly.

## Security considerations

- Do not forward 8006, 3001, 8096, or 22 directly on the router.
- Restrict tailnet membership and review access control rules.
- Proxmox still needs its own strong account credentials and TOTP.
- Tailscale connectivity requires the endpoints to be online with working underlying internet connectivity.
- Serving HTTP apps over a private encrypted tunnel is not equivalent to end-to-end HTTPS at the application layer; evaluate threat model and HTTPS options for sensitive future apps.
