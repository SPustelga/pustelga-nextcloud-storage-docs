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

Primary Nextcloud user:

```text
pustelga
```

Additional admin user:

```text
kovarsky
```

`kovarsky` is in the `admin` group, has unlimited quota, and has full-permission shares from `pustelga` for the main top-level folders so they appear in the Files app.

Shared folders:

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
nextcloud-qbittorrent-1   qbittorrent
nextcloud-redis-1         redis
```

Published ports:

```text
80/tcp and 443/tcp via Caddy
6881/tcp and 6881/udp for qBittorrent
192.168.1.42:8080/tcp for qBittorrent Web UI
```

## Version

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

## Notes And Tasks

Notes API was verified with the current app password:

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
