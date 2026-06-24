# Storage

## Disk Layout

Important paths on the Nextcloud VM:

```text
/opt/nextcloud
  Docker Compose project for Nextcloud, database, Redis, qBittorrent, and helpers.

/opt/nextcloud/data/pustelga/files
  Main Nextcloud data tree for user pustelga.

/mnt/nextcloud-extra
  Added 2 TB ext4 disk used for large personal data.
```

Last verified disk state after the June GIF transfer and staging cleanup:

```text
/dev/mapper/ubuntu--vg-ubuntu--lv  490G   95G  374G  21% /
/dev/sdb1                          2.0T  644G  1.2T  35% /mnt/nextcloud-extra
```

The large folders are intentionally backed by the added disk rather than the root filesystem.

## Persistent Mounts And Bind Mounts

Known active bind-mounted user folders:

```text
/mnt/nextcloud-extra/files/Torrents
  -> /opt/nextcloud/data/pustelga/files/Torrents

/mnt/nextcloud-extra/nextcloud-data/pustelga-files/Yandex
  -> /opt/nextcloud/data/pustelga/files/Yandex

/mnt/nextcloud-extra/nextcloud-data/pustelga-files/gifs
  -> /opt/nextcloud/data/pustelga/files/gifs
```

These paths make the user-visible Nextcloud folders live physically on the added 2 TB disk:

```text
/Torrents
/Yandex
/gifs
```

After changing files directly on disk, run a targeted scan:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ files:scan --path='pustelga/files/Torrents'
sudo docker compose exec -T -u www-data app php occ files:scan --path='pustelga/files/Yandex'
sudo docker compose exec -T -u www-data app php occ files:scan --path='pustelga/files/gifs'
```

## Nextcloud External Storage

Nextcloud external storage has been used to expose the added disk as a separate mount:

```text
Mount Point: /HDD 2TB
Storage: Local
Configuration root: /mnt/nextcloud-extra/files
Applicable user: pustelga
```

Useful user-visible paths:

- `/Torrents`: direct user folder backed by the new disk through a bind mount.
- `/Yandex`: Yandex Disk mirror backed by the new disk through a bind mount.
- `/gifs`: large GIF library backed by the new disk through a bind mount.
- `/HDD 2TB`: external storage root for the added disk, when enabled.

## Scan Timer

`nextcloud-scan-torrents.timer` runs every 5 minutes for torrent visibility:

```ini
[Timer]
OnBootSec=2min
OnUnitActiveSec=5min
Persistent=true
```

The service runs:

```bash
cd /opt/nextcloud
docker compose exec -T --user www-data app php occ files:scan --path='pustelga/files/Torrents' >/var/log/nextcloud-scan-torrents.log 2>&1
```

Manual torrent scan:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ files:scan --path='pustelga/files/Torrents'
```

Full external storage scan:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ files_external:scan 1 -vvv
```

## Space Reclaim

After moving/deleting large temporary archives or staging folders, run TRIM:

```bash
sudo fstrim -av
```

Examples from prior maintenance:

```text
/: 47.3 GiB trimmed after deleting a temporary GIF tar
/mnt/nextcloud-extra: 94.4 GiB trimmed after deleting GIF staging
```

## Safety Rules

- Never delete local source files until the server-side copy is verified.
- For large transfers, compare relative path lists or use a `missing=0` verification before cleanup.
- Run `occ files:scan` after direct filesystem changes.
- Keep secrets out of this repository. Use local env files and app passwords only.
