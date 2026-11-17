# 02 — Debian 13 VM setup

## Actual configuration

| Setting | Recorded value |
| --- | --- |
| Guest OS | Debian GNU/Linux 13 (Trixie) |
| RAM (original) | 2 GB |
| RAM (current) | 4 GB |
| Virtual CPUs | 2 |
| Guest root filesystem | ~116 GB shown by `df -h` |
| Administration | Headless, via SSH |
| Guest integration | QEMU Guest Agent enabled |

An initial desktop-environment installation was later removed to keep the guest lightweight.

## Setup outline

1. Create a Linux VM in Proxmox and attach the Debian installation ISO.
2. Assign a reasonable CPU, RAM, disk, and bridged network interface.
3. Install Debian and choose the necessary server packages; avoid an unnecessary desktop environment.
4. Create a non-root administration account.
5. Install guest integration and enable its service:

```bash
sudo apt update
sudo apt install -y qemu-guest-agent
sudo systemctl enable --now qemu-guest-agent
```

6. In Proxmox, enable the VM's QEMU Guest Agent option, then reboot the VM if necessary.
7. Confirm a working network interface and DNS resolution.

## Verification

```bash
cat /etc/os-release
hostname -I
ip -br address
free -h
nproc
df -h /
systemctl is-active qemu-guest-agent
```

**Note about IPs:** The guest's LAN IP was observed to change during the project. Do not assume a previous address is permanent. Consider a DHCP reservation on the router for predictable LAN access.

## Resource planning

With only ~8 GB of *physical* RAM on the host, assigning 4 GB to Debian leaves room for Proxmox and other overhead. There is no spare capacity for unlimited VMs or heavy machine-learning workloads. Monitor host pressure before increasing guest resources.

## Verification versus assumptions

The examples are reproducible guidance; they are not a literal transcript of every installer screen used during the original build.
