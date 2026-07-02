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
- Memos LAN URL: `http://192.168.1.42:5230`
- Public Memos URL: `https://memos.pustelga.xyz`
- Telegram bot: Nextcloud uploads plus Memos quick capture

## What Is Documented

- `docs/storage.md`: disks, mounts, bind mounts, external storage, scan commands.
- `docs/gifs.md`: `/gifs` library, transfer procedure, scan/verification notes.
- `docs/nextcloud.md`: Docker services, enabled apps, mobile login notes.
- `docs/memos.md`: Memos deployment on the Nextcloud VM.
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

## Current Availability

As of `2026-07-02`:

```text
https://nextcloud.pustelga.xyz/status.php: HTTP 200
https://memos.pustelga.xyz/: HTTP 200
http://192.168.1.42:5230: HTTP 200 for Memos
/gifs physical path: /opt/nextcloud/data/pustelga/files/gifs
/gifs verified state: 175367 files, about 439G, scan Errors 0
```

`memos.pustelga.xyz` is published through Caddy on the Nextcloud VM. Router forwarding stays limited to public HTTP/HTTPS; Memos port `5230` is not forwarded directly.
