# Pustelga Nextcloud Storage

Operational documentation for the personal Nextcloud storage stack.

This repository intentionally does not store passwords, app tokens, QR codes, database dumps, private notes, or file contents. Secret locations and handling rules are documented in `docs/security.md`.

## Current Topology

- VM host platform: Hyper-V
- VM LAN address: `192.168.1.42`
- Public Nextcloud URL: `https://nextcloud.pustelga.xyz`
- Compose project path on VM: `/opt/nextcloud`
- Primary Nextcloud user: `pustelga`
- Main added data disk mount: `/mnt/nextcloud-extra`
- Main Nextcloud data path: `/opt/nextcloud/data/pustelga/files`
- Large GIF library path: `/opt/nextcloud/data/pustelga/files/gifs`
- Torrent folder in Nextcloud: `/Torrents`
- Joplin sync folder in Nextcloud: `/Joplin`

## What Is Documented

- `docs/storage.md`: disks, mounts, bind mounts, external storage, scan commands.
- `docs/gifs.md`: `/gifs` library, transfer procedure, scan/verification notes.
- `docs/nextcloud.md`: Docker services, enabled apps, mobile login notes.
- `docs/torrents.md`: qBittorrent setup and visibility troubleshooting.
- `docs/joplin.md`: Joplin sync and Telegram-to-Joplin bridge.
- `docs/telegram-bot.md`: Telegram capture bot that uploads to Nextcloud and sends GIFs.
- `docs/operations.md`: common maintenance commands.
- `docs/security.md`: credential handling and local-only secret locations.
- `docs/changelog.md`: changes performed during setup and maintenance.

## Fast Checks

```bash
ssh sb@192.168.1.42
cd /opt/nextcloud
docker compose ps
docker compose exec -T -u www-data app php occ status
docker compose exec -T -u www-data app php occ files_external:list --all
systemctl status telegram-nextcloud-bot.service --no-pager -l
```

## Known Availability Note

As of `2026-06-24`, the Telegram bot code was updated, but the Nextcloud host was not reachable from the VPS or the Windows workstation:

```text
192.168.1.42 ping/SSH: timeout
https://nextcloud.pustelga.xyz/status.php: timeout from workstation
VPS -> nextcloud.pustelga.xyz: No route to host / connect timeout
```

When this happens, bot features that read Nextcloud over WebDAV, including the random GIF button, cannot work until the VM/network/public route is restored.
