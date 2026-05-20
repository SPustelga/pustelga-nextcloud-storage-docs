# Storage

## Disk Layout

Current important block devices:

```text
/dev/sda1 ext4 label=nextcloud-data uuid=8f03d933-dae2-4657-b954-b89f9b6dbc2f
  mounted at /mnt/nextcloud-extra
  also bind-mounted at /opt/nextcloud/data/pustelga/files/Torrents

/dev/sdb3 LVM root volume
  mounted at /
```

At inspection time `/dev/sda1` had about `1.7T` free and `5%` used.

## Persistent Mounts

Relevant `/etc/fstab` entries:

```fstab
UUID=8f03d933-dae2-4657-b954-b89f9b6dbc2f /mnt/nextcloud-extra ext4 defaults,nofail 0 2
/mnt/nextcloud-extra/files/Torrents /opt/nextcloud/data/pustelga/files/Torrents none bind,nofail 0 0
```

The bind mount makes the Nextcloud user folder `/Torrents` physically live on the added disk.

## Nextcloud External Storage

Nextcloud external storage is enabled and exposes the same disk as a separate mount:

```text
Mount ID: 1
Mount Point: /HDD 2TB
Storage: Local
Configuration: datadir=/mnt/nextcloud-extra/files
Applicable user: pustelga
```

This gives two useful paths:

- `/Torrents`: direct user folder backed by the new disk through a bind mount.
- `/HDD 2TB`: external storage root for the added disk.

## Scan Timer

`nextcloud-scan-torrents.timer` runs every 5 minutes:

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

Manual scan:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ files:scan --path='pustelga/files/Torrents'
```

Full external storage scan:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ files_external:scan 1 -vvv
```

