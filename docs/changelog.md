# Changelog

## 2026-05-20

- Moved the Telegram bot runtime from the Windows host to VPS `163.5.180.88`.
- Enabled `telegram-nextcloud-bot.service` on the VPS and disabled the old Windows Startup command.
- Created the Nextcloud admin user `kovarsky` and shared the main `pustelga` folders to it with full permissions.
- Updated the Joplin bridge to auto-start Joplin Desktop when the local API is unavailable.
- Enabled Joplin Web Clipper API on Windows and created the Joplin notebook `Telegram`.
- Added a Windows Nextcloud-to-Joplin bridge for Telegram text notes.
- Extended the Joplin bridge so Telegram `todo ...` messages create real Joplin todo items.
- Changed the Telegram `☑️` button to capture the next plain message as a Joplin todo.
- Deployed Telegram to Nextcloud WebDAV bot as `telegram-nextcloud-bot.service`.
- Disabled the VM bot service after detecting that the VM cannot reach Telegram API.
- Started the Telegram bot runner on Windows with Startup-folder autostart.
- Changed the Telegram bot start UI to a long inline action button.
- Added emoji labels to Telegram bot buttons and save confirmations.
- Changed GIFs inline button so it sends the latest saved GIFs directly into Telegram.
- Pointed the GIFs inline button at the root /gifs Nextcloud folder.
- Rotated GIF button responses so repeated presses send the next batch.
- Changed the GIF button to pick one random item from `/gifs` on every press.
- Replaced long Telegram inline actions with square emoji buttons.
- Added a todo-list button and `todo ...` capture into `/Telegram Notes/TODO.md`.
- Added native Telegram menu commands for start, gifs, and clear.
- Routed Telegram GIF/animation messages to /Telegram Uploads/GIFs/YYYY-MM-DD/.
- Created `/Telegram Uploads` and `/Telegram Notes`.
- Created a dedicated Nextcloud app password for the Telegram bot.
- Created `/Joplin` for Joplin WebDAV sync.
- Created a dedicated Joplin app password and verified WebDAV read/write/delete.
- Documented Joplin Desktop/Mobile sync settings and encryption flow.
- Verified Nextcloud Notes server-side API works with the current app password.
- Revoked exposed/broken app tokens.
- Deleted an accidental note that contained an app password.
- Reset Nextcloud bruteforce attempts for the reverse proxy source IP.
- Created a fresh app password and regenerated the local QR file.
- Confirmed Tasks app is installed and enabled.
- Confirmed `Tasks` CalDAV calendar/list exists for `pustelga`.
- Confirmed torrent scan timer runs every 5 minutes.
- Documented storage, torrents, operations, and security model.

## Earlier Setup

- Added a new Hyper-V disk to the VM.
- Formatted it as ext4 with label `nextcloud-data`.
- Mounted it at `/mnt/nextcloud-extra`.
- Added `/HDD 2TB` as a Nextcloud local external storage mount for user `pustelga`.
- Bind-mounted `/mnt/nextcloud-extra/files/Torrents` to `/opt/nextcloud/data/pustelga/files/Torrents`.
- Deployed qBittorrent in the Nextcloud compose project.
- Configured qBittorrent downloads to land on the added disk.
- Added automatic `occ files:scan` timer for the `Torrents` folder.
