# 03 — SSH administration

## Purpose

Manage the Debian VM from another computer without using the Proxmox console for routine work.

## Installation and service checks

On Debian:

```bash
sudo apt update
sudo apt install -y openssh-server
sudo systemctl enable --now ssh
sudo systemctl status ssh
hostname -I
```

From an authorized workstation on the same LAN:

```bash
ssh <DEBIAN_USER>@<DEBIAN_LAN_IP>
```

From a device connected to the same Tailscale network:

```bash
ssh <DEBIAN_USER>@<DEBIAN_TAILSCALE_IP>
```

The lab verified SSH from both a laptop and an Android terminal app. **Use a non-root account** and consider SSH key authentication as a follow-up security improvement.

## Key-based access (recommended follow-up)

On the client device:

```bash
ssh-keygen -t ed25519
ssh-copy-id <DEBIAN_USER>@<DEBIAN_LAN_IP>
```

Test key-based login **before** considering disabling password authentication. Keep a recovery path through the Proxmox console.

## Troubleshooting timeout

The actual build encountered `ssh: connect to host ... port 22: Connection timed out` after using a stale guest IP.

```bash
# In the Proxmox VM console
hostname -I
systemctl is-active ssh

# On the client
ping -c 4 <DEBIAN_LAN_IP>
ssh -v <DEBIAN_USER>@<DEBIAN_LAN_IP>
```

Inspect Proxmox VM network attachment, firewall policies, and LAN routing if needed. **A timeout is not the same as an authentication failure.**

## Security notes

- Keep port 22 private; do not forward it on the home router for this use case.
- Treat SSH keys as credentials and do not commit them.
- A login prompt does not by itself prove transport paths are fully hardened.
