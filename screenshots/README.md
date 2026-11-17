# Screenshots and demonstrations

The published videos are listed in the [Video Documentation Series](../README.md#video-documentation-series). The slots below are for future captures from the actual homelab.

| Planned filename | Capture | Review before sharing |
| --- | --- | --- |
| `proxmox-dashboard.png` | Proxmox dashboard with guest status | Hide node names, VM IDs, URLs, and browser account details |
| `debian-vm-configuration.png` | Debian VM hardware view showing 4 GB RAM and 2 vCPUs | Hide VM identifiers, storage pool names, and device addresses |
| `docker-containers.png` | `docker ps` output showing the two services | Hide bind addresses, shell prompts, and unrelated containers |
| `uptime-kuma-dashboard.png` | Monitor status with generic labels | Hide targets, notification settings, and internal names |
| `tailscale-connectivity.png` | Device connectivity with generic role labels | Hide account details, tailnet names, device names, and addresses |
| `jellyfin-media-library.png` | Empty or demo Movies and Shows view | Hide personal titles, artwork, watch history, and account details |
| `remote-access-android.png` | Successful access over mobile data | Hide the server URL, device identifiers, and phone notifications |

## Capture and sanitization

1. Capture the smallest area that proves the feature. Close unrelated tabs and hide notifications before recording.
2. Crop or apply opaque redaction to private addresses, DNS names, device IDs, emails, usernames, and media titles. Keep screens with credentials, API tokens, Telegram chat IDs, recovery keys, and session cookies out of the capture.
3. Export a flattened image, strip metadata, and reopen the exported file. Review it at full size and confirm redactions cannot be removed. For video, review frames, audio, and captions.
4. Label each capture as live evidence or a demonstration using sample data. Keep original files outside the repository.

Common image extensions are ignored by default. Stage an approved capture with `git add -f -- screenshots/<SAFE_IMAGE_NAME>.png` and inspect the staged file list before committing. See the [security policy](../SECURITY.md) for the publication review.
