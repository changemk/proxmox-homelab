# Security and disclosure policy

This is a **public educational portfolio**, not a place to report vulnerabilities in third-party software. For product vulnerabilities, follow each upstream project's coordinated disclosure process.

## Before sharing this repository

- Never commit passwords, bot tokens, recovery codes, Tailscale auth keys, SSH private keys, or backup encryption keys.
- Use illustrative hostnames/IPs rather than real tailnet or internal addresses.
- Do not include screenshots with personal emails, machine identifiers, media filenames, tokens, browser sessions, or device inventory.
- Exclude Docker volumes, databases, photo/video files, and application configuration directories.
- Review the *entire Git history*, not just the latest commit, before publishing.
- If a credential was ever committed, revoke/rotate it; removing a file does not invalidate the exposed secret.

## Operating principles

- Never forward administrative ports (such as Proxmox 8006 or SSH 22) directly from the internet unless there is a deliberately engineered, documented need and additional controls.
- Use Tailscale / a trusted private path for remote administration; limit tailnet membership and access policies.
- Use Proxmox TOTP 2FA and store recovery keys securely, outside this repository.
- Keep the Proxmox host and Debian guest updated; maintain independent backups.
- Docker access via the `docker` group is **root-equivalent** on the Debian VM.
- Docker `ports:` mappings bind to all interfaces by default unless a bind address is supplied. Review the host firewall and Docker networking when deciding service exposure.

This repository does not claim a complete security audit or production hardening certification.
