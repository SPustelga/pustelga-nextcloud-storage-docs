# Memos

## Purpose

Memos is deployed as a lightweight self-hosted micro-note system on the same VM as Nextcloud.

It is intended as a possible replacement for the Joplin capture flow:

- short notes and thoughts;
- Markdown-style quick capture;
- attachments through the Memos UI/API;
- mobile use through web/PWA or mobile clients;
- Telegram bot capture for text, todos, and uploaded media links.

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

Public URL:

```text
https://memos.pustelga.xyz
```

LAN URL:

```text
http://192.168.1.42:5230
```

Published port:

```text
192.168.1.42:5230 -> container port 5230
```

The port is bound to the VM LAN address only. It is not published as `0.0.0.0`.

Public access is handled by Caddy on the Nextcloud VM:

```text
memos.pustelga.xyz -> memos:5230
```

DNS:

```text
memos.pustelga.xyz A 84.201.247.104
```

Router forwarding uses the existing public `80/443` rules to the Nextcloud VM. Do not expose port `5230` directly to the internet.

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

HTTP check from public HTTPS:

```bash
curl -I https://memos.pustelga.xyz/
```

Caddy config validation:

```bash
cd /opt/nextcloud
sudo docker compose exec -T caddy caddy validate --config /etc/caddy/Caddyfile
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

## Telegram Bot Integration

The Telegram bot saves durable files to Nextcloud and creates Memos notes through the Memos API.

Current flow:

```text
Telegram text -> Memos memo
Telegram memo button -> next text becomes Memos memo
Telegram photo/file -> Nextcloud upload -> Memos memo with attachment plus the Nextcloud path
Telegram todo button -> Memos memo with #todo and Markdown checkbox
Telegram tags -> #idea, #todo, #inbox, #photo
```

API base:

```text
https://memos.pustelga.xyz/api/v1
```

The bot uses a Memos personal access token created for user `serb`. The token itself is stored only on the VPS in:

```text
/etc/telegram-nextcloud-bot.env
```

Relevant env variables:

```text
MEMOS_BASE_URL=https://memos.pustelga.xyz
MEMOS_TOKEN=redacted
MEMOS_VISIBILITY=PRIVATE
```

Smoke test performed after deploy:

```text
Memos auth: HTTP 200
temporary memo create/delete: HTTP 200
temporary attachment create/delete through bot code: ok
Telegram menu message: sent
```
