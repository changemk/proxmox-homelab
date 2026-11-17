# 08 — Security hardening

## Implemented security choices

- **Proxmox TOTP 2FA** enabled and login tested.
- **Tailscale private networking** used for remote management of both the hypervisor and Debian.
- **No router port forwarding** was required for remote admin use.
- **OpenSSH** used for Debian administration.
- Personal credentials and media are excluded from this public repository.

## Proxmox two-factor authentication

Within the Proxmox UI, use **Datacenter → Permissions → Two Factor** (location can vary by version) to configure TOTP on the relevant administrative account.

- Enroll a trusted authenticator.
- Test signing in with the generated one-time code in a **new session** before closing the current session.
- Store recovery codes securely outside the homelab and outside this repository.

## Recommended checks

### SSH

```bash
sudo systemctl status ssh
sudo ss -lntp
```

Consider migrating to SSH public-key authentication and restricting password login **only after** confirming an emergency console access method.

### Docker security

- `docker` group membership grants root-equivalent permissions.
- Review host binds and published ports; Docker Compose mappings like `8096:8096` commonly listen on all available host interfaces.
- Keep application admin passwords unique.
- Review release notes and back up data before major container upgrades.

### Network security

- Restrict Tailscale devices and network policy to least privilege.
- Disable unnecessary service exposure; use a firewall policy aligned with Docker's packet handling.
- Understand that a self-signed Proxmox browser warning does not establish the legitimacy of the server; validate certificates or use trusted certificate management if appropriate.

## Future improvements — not claimed as complete

- SSH keys and carefully tested password-authentication restrictions
- Explicit firewall rules and tailnet access policies
- Encrypted off-host backups and routine restore tests
- Automated OS update planning and vulnerability review
- Documented incident response and secret rotation

This is a learning homelab, not a certified production deployment.
