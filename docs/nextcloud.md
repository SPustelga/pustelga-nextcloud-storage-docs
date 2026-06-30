# Nextcloud

## Deployment

The stack runs from:

```text
/opt/nextcloud
```

Public URL:

```text
https://nextcloud.pustelga.xyz
```

VM LAN address:

```text
192.168.1.42
```

Primary Nextcloud user:

```text
pustelga
```

Additional admin user:

```text
kovarsky
```

`kovarsky` is in the `admin` group, has unlimited quota, and has full-permission shares from `pustelga` for the main top-level folders so they appear in the Files app.

Shared folders include:

```text
Camera Uploads
gifs
Joplin
Mail.ru
Photos
Share
Telegram Joplin Queue
Telegram Notes
Telegram Uploads
Torrents
Yandex
Заметки
```

## Services

Current compose services:

```text
nextcloud-app-1           app
nextcloud-caddy-1         caddy
nextcloud-cron-1          cron
nextcloud-db-1            db
nextcloud-memos-1         memos
nextcloud-qbittorrent-1   qbittorrent
nextcloud-redis-1         redis
```

Published ports:

```text
80/tcp and 443/tcp via Caddy
6881/tcp and 6881/udp for qBittorrent
192.168.1.42:8080/tcp for qBittorrent Web UI
192.168.1.42:5230/tcp for Memos
```

## Memos

Memos is deployed on the same VM as a separate Docker Compose service.

Current LAN URL:

```text
http://192.168.1.42:5230
```

Data path:

```text
/opt/nextcloud/memos
```

Public DNS for `memos.pustelga.xyz` is not configured yet, so Memos is currently LAN-only.

## Version

Last documented version:

```text
Nextcloud 31.0.14
maintenance: false
needsDbUpgrade: false
```

## Enabled Apps Of Interest

```text
activity: 4.0.0
dav: 1.33.0
files_external: 1.23.1
notes: 5.0.0
photos: 4.0.0
tasks: 0.17.1
```

## Important User Folders

Large folders backed by the added 2 TB disk:

```text
/Torrents
/Yandex
/gifs
```

GIF library last verified after scan on `2026-06-15`:

```text
/gifs
files: 111801
size: 282G
scan errors: 0
```

Joplin sync folder:

```text
/Joplin
```

Telegram capture folders:

```text
/Telegram Notes
/Telegram Uploads
/Telegram Joplin Queue
```

## Notes And Tasks

Notes API was verified with a dedicated app password:

```text
Notes API: 200
Capabilities endpoint: 200
```

Tasks has a CalDAV calendar/list named `Tasks`, created for the `pustelga` user. Android task sync should be done with DAVx5 plus a task app such as Tasks.org. The regular Nextcloud Android app is mainly for files and account integration.

## Mobile Login

Use an app password for mobile clients. Do not reuse the web login password in random mobile integrations.

Local helper files on the operator workstation:

```text
E:\Codex\NextCloud\nextcloud-mobile-app-password.txt
E:\Codex\NextCloud\nextcloud-login-qr.png
```

These files are intentionally not included in this repository.

## Availability Note

As of `2026-06-24`, the documentation was updated while the Nextcloud host was not reachable from the current workstation or the VPS:

```text
ping 192.168.1.42: timeout
ssh sb@192.168.1.42: timeout
https://nextcloud.pustelga.xyz/status.php: timeout from workstation
VPS curl https://nextcloud.pustelga.xyz/status.php: connect timeout / no route
```

This is an infrastructure/network availability issue, not an empty `/gifs` folder issue. Restore VM/network/public route first, then recheck WebDAV and bot functions.
