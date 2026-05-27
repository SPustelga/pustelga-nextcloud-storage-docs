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

Telegram bots cannot initiate a new chat by themselves. The user must open the bot in Telegram and press Start or send `/start` once. After that the bot sends one compact row of square inline buttons:

```text
🚀 start
🎲 random GIF from /gifs
☑️ capture next message as todo
🧹 clear recent chat
```

Telegram clients render inline buttons as blue action buttons under the message. The `🎲` button picks one random GIF/animation file from `/gifs` and sends it directly back into the Telegram chat.

The `☑️` button switches the bot into todo capture mode. After pressing it, the next plain text message is saved as a todo without requiring a `todo` prefix. Sending `todo buy milk` or `туду купить молоко` still works too.

Bot responses use visual prefixes such as `📝`, `🖼`, `📎`, `🎙`, `🎧`, `🎬`, and `🎞` to make saved item confirmations easier to scan.


## Native Telegram Menu

The bot has a built-in Telegram menu button configured through Bot API commands:

```text
/start - 🚀 старт и кнопки
/gifs  - 🎞 прислать GIFs из /gifs
/clear - 🧹 очистить чат
/todo  - ☑️ show todo-list
```

The menu button is the native Telegram command menu near the message input field.

`/clear` attempts to delete the recent chat history visible to the bot. Telegram may refuse deleting older messages or messages it has no permission to remove. The bot handles Telegram HTTP 429 rate limits by waiting for `retry_after` and retrying; chat clearing is capped to a small batch and throttled to avoid flooding the API.

## Nextcloud Paths

Base WebDAV URL:

```text
https://nextcloud.pustelga.xyz/remote.php/dav/files/pustelga
```

Saved text notes:

```text
/Telegram Notes/YYYY-MM-DD/*.md
```

Every normal text message is also queued for Joplin and then imported into the Joplin notebook `Telegram` by the Windows bridge. Todo messages, including messages captured after pressing `☑️`, are queued as Joplin todo items with `is_todo=1`.

Joplin queue:

```text
/Telegram Joplin Queue/pending/*.json
/Telegram Joplin Queue/done/*.json
/Telegram Joplin Queue/failed/*.json
```

Saved uploads:

```text
/Telegram Uploads/YYYY-MM-DD/*
```

Source folder for the `🎲` button:

```text
/gifs
```

Pressing `🎲` sends one random GIF/animation file from that folder into Telegram. Every sent GIF includes a `🎲 Хочу еще` button under it, which requests another random GIF. The bot scans the `/gifs` root plus a small random sample of its subfolders on demand, so newly added files can be picked without restarting the bot while keeping button response time reasonable.

Todo-list file:

```text
/Telegram Notes/TODO.md
```

New Telegram GIF/animation messages sent to the bot are still archived here:

```text
/Telegram Uploads/GIFs/YYYY-MM-DD/*
```

## Current Runtime

The active bot runner is a systemd service on the VPS:

```text
163.5.180.88
```

The old Windows runner was stopped, and its Startup-folder command was renamed to prevent duplicate Telegram polling on Windows login:

```text
C:\Users\vodob\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\TelegramNextcloudBot.cmd.disabled
```

The Joplin bridge runs on the Windows host because Joplin Desktop exposes its REST API only on localhost:

```text
E:\Codex\NextCloud\run-joplin-nextcloud-bridge.ps1
E:\Codex\NextCloud\joplin_nextcloud_bridge.py
E:\Codex\NextCloud\joplin-nextcloud-bridge.windows.log
C:\Users\vodob\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\JoplinNextcloudBridge.cmd
```

## VPS Service

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

Current VPS service state:

```text
active
enabled
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

Stop:

```bash
sudo systemctl stop telegram-nextcloud-bot.service
```

## Security

The bot accepts messages only from the configured chat ID. Other chats get a short access denied response.

The current Nextcloud app token for the bot is stored in local runner configuration only and must not be committed.
