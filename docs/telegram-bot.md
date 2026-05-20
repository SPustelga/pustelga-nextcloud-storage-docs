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

Telegram bots cannot initiate a new chat by themselves. The user must open the bot in Telegram and press Start or send `/start` once. After that the bot shows a persistent keyboard with a `Старт` button.

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

## Service

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

Stop:

```bash
sudo systemctl stop telegram-nextcloud-bot.service
```

## Security

The bot accepts messages only from the configured chat ID. Other chats get a short access denied response.

The current Nextcloud app token for the bot is stored only in `/etc/telegram-nextcloud-bot.env`.

