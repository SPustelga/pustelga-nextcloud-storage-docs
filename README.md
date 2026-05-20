# Pustelga Nextcloud Storage

Operational documentation for the local Nextcloud storage VM.

This repository intentionally does not store passwords, app tokens, QR codes, database dumps, or private note/file contents. Local secret references are documented in `docs/security.md`.

## Current Topology

- VM host platform: Hyper-V
- VM LAN address: `192.168.1.42`
- Public Nextcloud URL: `https://nextcloud.pustelga.xyz`
- Compose project path on VM: `/opt/nextcloud`
- Primary Nextcloud user: `pustelga`
- Nextcloud version: `31.0.14`
- Added data disk: `/dev/sda1`, ext4 label `nextcloud-data`
- Added disk mount: `/mnt/nextcloud-extra`
- User-visible external storage: `/HDD 2TB`
- Torrent folder in Nextcloud: `/Torrents`

## What Is Documented

- `docs/storage.md`: disks, mounts, bind mounts, external storage, scan timer.
- `docs/nextcloud.md`: Docker services, enabled apps, mobile login notes.
- `docs/torrents.md`: qBittorrent setup and visibility troubleshooting.
- `docs/operations.md`: common maintenance commands.
- `docs/security.md`: credential handling and local-only secret locations.
- `docs/changelog.md`: changes performed during setup.

## Fast Checks

```bash
cd /opt/nextcloud
docker compose ps
docker compose exec -T -u www-data app php occ status
docker compose exec -T -u www-data app php occ files_external:list --all
systemctl status nextcloud-scan-torrents.timer
```

