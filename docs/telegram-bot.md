# Telegram To Nextcloud Bot

## Purpose

The bot receives Telegram messages and saves them to Nextcloud through WebDAV.

It is intended for quick capture:

- text messages become Markdown files;
- photos and documents become files;
- captions are saved next to uploads as Markdown;
- voice, audio, and video messages are stored as files;
- text, todos, photos, and files are also captured into Memos;
- plain text messages are also queued for import into Joplin;
- todo captures create Joplin todo items.

## Telegram

Bot username:

```text
kovarsky_action_bot
```

Allowed chat ID:

```text
8490226851
```

Telegram bots cannot initiate a new chat by themselves. The user must open the bot in Telegram and press Start or send `/start` once.

## Buttons

The bot has two button surfaces:

- a native Telegram command menu with `/start`, `/gif`, `/todo`, and `/clear`;
- a persistent reply keyboard near the message input area with the main actions.

Persistent reply keyboard:

```text
📝 Memo   ☑️ Todo
🎲 GIF    🧹 Очистить
🚀 Старт
```

The `🎲 GIF` button picks one random GIF/animation file from `/gifs` and sends it directly back into the Telegram chat.

Every sent GIF includes:

```text
🎲 Хочу еще GIF
📝 Memo
☑️ Todo
🏠 Главное меню
```

The `📝 Memo` button switches the bot into memo capture mode. After pressing it, the next plain text message is saved to Nextcloud and Memos with memo tags.

The `☑️ Todo` button switches the bot into todo capture mode. After pressing it, the next plain text message is saved as a todo without requiring a `todo` prefix. Sending `todo buy milk` or `туду купить молоко` still works too.

Bot responses use visual prefixes such as `📝`, `🖼`, `📎`, `🎙`, `🎧`, `🎬`, and `🎞` to make saved item confirmations easier to scan.

## Native Telegram Menu

The native Telegram menu button is enabled and uses bot commands:

```text
/start - show buttons
/gif - random GIF
/todo - add todo
/clear - clear recent bot chat messages where Telegram allows it
```

Telegram clients may cache the button/menu briefly. Reopen the chat or restart the Telegram client if the menu state looks stale.

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

Memos capture:

```text
text -> Memos memo with #telegram #inbox
Memo button text -> Memos memo with #telegram #memo
Todo button text -> Memos memo with #telegram #todo and Markdown checkbox
photo/file/media -> Memos memo with the saved Nextcloud path
```

Memos API base:

```text
https://memos.pustelga.xyz/api/v1
```

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

The `/gifs` library is nested. The bot now walks nested folders recursively with a bounded random search rather than checking only the top-level folder. Supported media extensions:

```text
.gif
.mp4
.webm
.mov
```

If WebDAV is unreachable, the bot reports a connectivity error instead of incorrectly saying `/gifs` is empty.

Todo-list file:

```text
/Telegram Notes/TODO.md
```

New Telegram GIF/animation messages sent to the bot are archived here:

```text
/Telegram Uploads/GIFs/YYYY-MM-DD/*
```

## Current Runtime

The active bot runner is a systemd service on the VPS:

```text
163.5.180.88
```

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

The env file contains the Telegram bot token, Nextcloud app password, and Memos personal access token, so it must not be committed.

Memos-related env variables:

```text
MEMOS_BASE_URL=https://memos.pustelga.xyz
MEMOS_TOKEN=redacted
MEMOS_VISIBILITY=PRIVATE
```

Current VPS service state after the `2026-07-02` deploy:

```text
active
```

## Windows Joplin Bridge

The Joplin bridge runs on the Windows host because Joplin Desktop exposes its REST API only on localhost:

```text
E:\Codex\NextCloud\run-joplin-nextcloud-bridge.ps1
E:\Codex\NextCloud\joplin_nextcloud_bridge.py
E:\Codex\NextCloud\joplin-nextcloud-bridge.windows.log
C:\Users\vodob\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\JoplinNextcloudBridge.lnk
```

The bridge checks `http://127.0.0.1:41184/ping` before processing the queue. If Joplin Desktop is not running, it starts:

```text
C:\Users\vodob\AppData\Local\Programs\Joplin\Joplin.exe
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

## Current Health

After the `2026-07-02` deploy:

```text
telegram-nextcloud-bot.service: active
bot Python compile check: ok
Memos auth check: HTTP 200
temporary Memos memo create/delete: HTTP 200
Telegram menu message delivery: ok
```

## Security

The bot accepts messages only from the configured chat ID. Other chats get a short access denied response.

The current Nextcloud app token for the bot is stored in local runner configuration and `/etc/telegram-nextcloud-bot.env` only. It must not be committed.
