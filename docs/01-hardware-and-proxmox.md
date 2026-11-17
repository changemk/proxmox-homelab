# 01 — Hardware and Proxmox VE

## Goal

Reuse an older laptop as a virtualization host while keeping application workloads isolated in a Linux virtual machine.

## Actual environment

- **Physical host:** Lenovo ThinkPad T460s
- **RAM:** approximately 8 GB installed on host
- **Storage:** 256 GB SSD (reported physical capacity; VM storage is allocated from available host space)
- **Hypervisor:** Proxmox VE, installed directly on hardware
- **Primary guest:** Debian 13 VM

The exact Proxmox version, storage backend, firmware configuration, and installation date are **not recorded** here. Capture these from the running host before adding specifics.

## Concept

Proxmox is the hypervisor management layer. The Debian guest is the application server. Services such as Jellyfin and Uptime Kuma run *inside Debian*, not on the Proxmox host itself.

```text
Physical ThinkPad
└── Proxmox VE
    └── Debian 13 VM
        └── Docker containers
```

## Reproduction outline

1. Check CPU virtualization support, disk health, and power/cooling behavior before installation.
2. Back up anything on the target disk; a Proxmox installation can erase it.
3. Download and verify the appropriate installer from [Proxmox](https://www.proxmox.com/en/downloads).
4. Install Proxmox VE on the dedicated machine, configure the management network, and set an administrative password.
5. Open the web UI at `https://<PROXMOX_IP>:8006` from the LAN.
6. Upload a verified Debian ISO to Proxmox storage and create a VM.

**Security:** Proxmox uses HTTPS on port 8006; a self-signed certificate may initially appear. Do not expose this port via router forwarding. In this lab, remote access is handled with Tailscale.

## Verification

From the Proxmox Shell:

```bash
pveversion
qm list
free -h
lsblk
```

Use the actual Proxmox documentation for version-specific settings: <https://pve.proxmox.com/pve-docs/>.

## Learning outcomes

- Host versus guest resource ownership
- VM provisioning and virtual networks
- Management-plane access and physical resource constraints
