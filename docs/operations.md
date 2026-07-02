# Operations

## SSH

VM LAN address:

```text
192.168.1.42
```

Admin/operator user:

```text
sb
```

Do not store the SSH password in this repository.

## Docker Compose

```bash
cd /opt/nextcloud
sudo docker compose ps
sudo docker compose logs --tail=200 app
sudo docker compose logs --tail=200 qbittorrent
sudo docker compose logs --tail=200 caddy
sudo docker compose logs --tail=200 memos
```

## Memos

Public URL:

```text
https://memos.pustelga.xyz
```

LAN URL:

```text
http://192.168.1.42:5230
```

Common checks:

```bash
cd /opt/nextcloud
sudo docker compose ps memos
sudo docker compose logs --tail=80 memos
curl -I http://192.168.1.42:5230/
curl -I https://memos.pustelga.xyz/
sudo docker compose exec -T caddy caddy validate --config /etc/caddy/Caddyfile
sudo docker compose logs --tail=80 caddy
```

Data directory:

```text
/opt/nextcloud/memos
```

Back up this directory before Memos upgrades.

## Nextcloud OCC

Run OCC inside the app container as `www-data`:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ status
sudo docker compose exec -T -u www-data app php occ app:list
sudo docker compose exec -T -u www-data app php occ files_external:list --all
```

## Rescan Files

Torrent folder:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ files:scan --path='pustelga/files/Torrents'
```

GIF library:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ files:scan --path='pustelga/files/gifs'
```

Yandex folder:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ files:scan --path='pustelga/files/Yandex'
```

External storage:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ files_external:scan 1 -vvv
```

## Verify Large Folder State

Count and size directly on the VM:

```bash
sudo find /opt/nextcloud/data/pustelga/files/gifs -type f | wc -l
sudo du -sh /opt/nextcloud/data/pustelga/files/gifs
df -h / /mnt/nextcloud-extra
```

Check WebDAV from a machine that can reach Nextcloud:

```bash
curl -u 'pustelga:APP_PASSWORD' -X PROPFIND -H 'Depth: 1' \
  https://nextcloud.pustelga.xyz/remote.php/dav/files/pustelga/gifs/
```

Expected status for a reachable folder:

```text
207 Multi-Status
```

## Network Troubleshooting

From the Windows workstation:

```powershell
ping 192.168.1.42
ssh sb@192.168.1.42
Invoke-WebRequest https://nextcloud.pustelga.xyz/status.php -UseBasicParsing
```

From the VPS that runs the Telegram bot:

```bash
getent ahosts nextcloud.pustelga.xyz
curl -4 -I --max-time 10 https://nextcloud.pustelga.xyz/status.php
journalctl -u telegram-nextcloud-bot.service -n 100 --no-pager
```

If `192.168.1.42` does not respond to ping or SSH, fix the Hyper-V VM/network before debugging WebDAV, the Telegram bot, or Joplin sync.

## Brute Force / Rate Limit Reset

If mobile clients repeatedly use a bad token, Nextcloud can answer `429`.

Check/reset for the reverse proxy container IP:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ security:bruteforce:attempts 172.18.0.5
sudo docker compose exec -T -u www-data app php occ security:bruteforce:reset 172.18.0.5
```

## App Passwords

Create a new app password using the real Nextcloud login password through `NC_PASS`:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data -e NC_PASS='REDACTED' app php occ user:auth-tokens:add --password-from-env pustelga
```

List or revoke app tokens:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ user:auth-tokens:list pustelga
sudo docker compose exec -T -u www-data app php occ user:auth-tokens:delete pustelga TOKEN_ID
```

## Telegram Bot

VPS service status:

```bash
systemctl status telegram-nextcloud-bot.service --no-pager -l
journalctl -u telegram-nextcloud-bot.service -f
```

Restart after deploying `/opt/telegram-nextcloud-bot/bot.py`:

```bash
sudo python3 -m py_compile /opt/telegram-nextcloud-bot/bot.py
sudo systemctl restart telegram-nextcloud-bot.service
systemctl is-active telegram-nextcloud-bot.service
```

The bot env file also contains the Memos API settings:

```text
MEMOS_BASE_URL=https://memos.pustelga.xyz
MEMOS_TOKEN=redacted
MEMOS_VISIBILITY=PRIVATE
```

Memos smoke test from the VPS:

```bash
source /etc/telegram-nextcloud-bot.env
curl -sS -H "Authorization: Bearer $MEMOS_TOKEN" \
  "$MEMOS_BASE_URL/api/v1/auth/me"
```

## Joplin Bridge

Windows-side checks:

```powershell
Invoke-WebRequest http://127.0.0.1:41184/ping -UseBasicParsing
Get-Content E:\Codex\NextCloud\joplin-nextcloud-bridge.windows.log -Tail 80
```

Expected API response:

```text
JoplinClipperServer
```
