# Telegram To Nextcloud Bot

## Purpose

The bot receives Telegram messages and saves them to Nextcloud through WebDAV.

It is intended for quick capture:

- text messages become Markdown files;
- photos and documents become files;
- captions are saved next to uploads as Markdown;
- voice, audio, and video messages are stored as files.

## Telegram

Bot username:

```text
kovarsky_action_bot
```

Allowed chat ID:

```text
8490226851
```

Telegram bots cannot initiate a new chat by themselves. The user must open the bot in Telegram and press Start or send `/start` once. After that the bot sends a message with a long inline `Start` button. Telegram clients render inline buttons as blue action buttons under the message.

## Nextcloud Paths

Base WebDAV URL:

```text
https://nextcloud.pustelga.xyz/remote.php/dav/files/pustelga
```

Saved text notes:

```text
/Telegram Notes/YYYY-MM-DD/*.md
```

Saved uploads:

```text
/Telegram Uploads/YYYY-MM-DD/*
```

## Current Runtime

The bot code was first installed on the VM, but the VM could not reach `api.telegram.org` while other sites were reachable. To avoid duplicate Telegram polling, the VM systemd service is currently disabled.

The active bot runner is on the Windows host:

```text
E:\Codex\NextCloud\run-telegram-nextcloud-bot.ps1
E:\Codex\NextCloud\telegram_nextcloud_bot.py
E:\Codex\NextCloud\telegram-nextcloud-bot.windows.log
```

Autostart is configured through the user's Startup folder:

```text
C:\Users\vodob\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\TelegramNextcloudBot.cmd
```

## VM Service

Systemd service:

```text
telegram-nextcloud-bot.service
```

Bot code:

```text
/opt/telegram-nextcloud-bot/bot.py
```

Environment file:

```text
/etc/telegram-nextcloud-bot.env
```

The env file contains the Telegram bot token and Nextcloud app password, so it must not be committed.

Current VM service state:

```text
inactive
disabled
```

## Operations

Status:

```bash
systemctl status telegram-nextcloud-bot.service --no-pager -l
```

Logs:

```bash
journalctl -u telegram-nextcloud-bot.service -f
```

Restart:

```bash
sudo systemctl restart telegram-nextcloud-bot.service
```

Windows runner log:

```powershell
Get-Content E:\Codex\NextCloud\telegram-nextcloud-bot.windows.log -Tail 50
```

Windows runner process:

```powershell
Get-CimInstance Win32_Process | Where-Object { $_.CommandLine -like '*telegram_nextcloud_bot.py*' }
```

Stop:

```bash
sudo systemctl stop telegram-nextcloud-bot.service
```

## Security

The bot accepts messages only from the configured chat ID. Other chats get a short access denied response.

The current Nextcloud app token for the bot is stored in local runner configuration only and must not be committed.
