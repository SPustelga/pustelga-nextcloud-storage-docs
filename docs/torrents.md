# Torrents

## qBittorrent

qBittorrent runs inside the same compose project as service `qbittorrent`.

Web UI:

```text
http://192.168.1.42:8080
```

The Web UI password is stored only locally on the operator workstation:

```text
E:\Codex\NextCloud\qbittorrent-webui-password.txt
```

Do not commit that file.

## Download Path

Downloads are stored on the added disk:

```text
/mnt/nextcloud-extra/files/Torrents
```

The same path is bind-mounted into the Nextcloud user files tree:

```text
/opt/nextcloud/data/pustelga/files/Torrents
```

User-visible paths:

```text
/Torrents
/HDD 2TB/Torrents
```

## Why Files May Temporarily Disappear

Nextcloud does not automatically notice every filesystem-level change made by qBittorrent. The scan timer normally fixes this within about 5 minutes.

If folders are missing in the web UI or mobile app:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ files:scan --path='pustelga/files/Torrents'
```

Then verify through WebDAV or the web UI.

## Useful Checks

```bash
findmnt /mnt/nextcloud-extra
findmnt /opt/nextcloud/data/pustelga/files/Torrents
ls -la /mnt/nextcloud-extra/files/Torrents
systemctl status nextcloud-scan-torrents.timer
tail -100 /var/log/nextcloud-scan-torrents.log
```

