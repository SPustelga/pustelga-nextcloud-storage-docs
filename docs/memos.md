# Memos

## Purpose

Memos is deployed as a lightweight self-hosted micro-note system on the same VM as Nextcloud.

It is intended as a possible replacement for the Joplin capture flow:

- short notes and thoughts;
- Markdown-style quick capture;
- attachments through the Memos UI/API;
- mobile use through web/PWA or mobile clients;
- future Telegram bot integration.

## Deployment

Compose project:

```text
/opt/nextcloud
```

Service:

```text
memos
```

Container:

```text
nextcloud-memos-1
```

Image:

```text
neosmemo/memos:stable
```

Version observed in logs after first start:

```text
Memos 0.29.1
```

Data path on the VM:

```text
/opt/nextcloud/memos
```

Database:

```text
/opt/nextcloud/memos/memos_prod.db
```

## Access

Current LAN URL:

```text
http://192.168.1.42:5230
```

Published port:

```text
192.168.1.42:5230 -> container port 5230
```

The port is bound to the VM LAN address only. It is not published as `0.0.0.0`.

Planned public URL:

```text
https://memos.pustelga.xyz
```

DNS for `memos.pustelga.xyz` was not present when Memos was installed. Add an A record to the same public address used by `nextcloud.pustelga.xyz`, then add a Caddy reverse proxy block.

## Compose Snippet

```yaml
  memos:
    image: neosmemo/memos:stable
    restart: unless-stopped
    volumes:
      - ./memos:/var/opt/memos
    ports:
      - "192.168.1.42:5230:5230"
```

## Checks

Service state:

```bash
cd /opt/nextcloud
sudo docker compose ps memos
sudo docker compose logs --tail=80 memos
```

HTTP check from LAN:

```bash
curl -I http://192.168.1.42:5230/
```

Expected:

```text
HTTP 200
```

## Backups

Back up the whole Memos data directory:

```text
/opt/nextcloud/memos
```

At minimum, preserve:

```text
memos_prod.db
memos_prod.db-wal
memos_prod.db-shm
```

For a clean SQLite backup, stop the container or use a SQLite online backup command before copying.

## Telegram Bot Integration Plan

The existing Telegram bot currently saves files to Nextcloud and has a legacy Joplin queue.

Possible Memos replacement flow:

```text
Telegram text -> Memos memo
Telegram photo/file -> upload attachment -> Memos memo with attachment/link
Telegram todo button -> Memos memo with #todo and Markdown checkbox
Telegram tags -> #idea, #todo, #inbox, #photo
```

Keep Nextcloud as the durable file store for large attachments if needed, and store links in Memos.
