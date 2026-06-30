# Changelog

## 2026-06-30

- Documented Memos deployment on the Nextcloud VM.
- Memos runs as `nextcloud-memos-1` from `/opt/nextcloud` using `neosmemo/memos:stable`.
- Memos data is stored in `/opt/nextcloud/memos`.
- Memos is currently LAN-only at `http://192.168.1.42:5230`; DNS for `memos.pustelga.xyz` is not configured yet.
- Verified Nextcloud status endpoint returned HTTP `200`.
- Verified Memos returned HTTP `200`.
- Recorded pre-transfer state for the next local GIF batch from `E:\Codex\NextCloud\gifs`: `63402` files, about `168.1G`.
- Updated Telegram bot documentation to reflect the native commands menu plus persistent reply keyboard.
- Transferred the local GIF batch from `E:\Codex\NextCloud\gifs` into Nextcloud `/gifs`.
- Verified `63566` local files against final `/gifs` by relative path and file size; missing files: `0`.
- Ran `occ files:scan --path='pustelga/files/gifs'`; scan completed with `Errors 0` and `64451` updated files.
- Verified final `/gifs` state: `175367` files, about `439G`.
- Deleted `63566` verified local files from `E:\Codex\NextCloud\gifs`; local files remaining: `0`.
- Removed server staging `/mnt/nextcloud-extra/nextcloud-data/gifs-upload-20260630` and ran `fstrim`.
- Verified the 2 TB disk after cleanup: `/dev/sdb1` about `801G` used and `1.1T` free.

## 2026-06-24

- Updated Telegram bot UI to use wide inline buttons under messages.
- Removed the native Telegram menu/command button by clearing bot commands and resetting the chat menu button.
- Changed GIF reply controls to include `🎲 Хочу еще GIF` and `🏠 Главное меню`.
- Fixed GIF lookup logic so the bot searches nested `/gifs` folders instead of treating a nested library as empty.
- Changed GIF error handling so WebDAV connectivity failures are reported as connectivity errors, not as an empty folder.
- Confirmed the bot service on VPS `163.5.180.88` is active after deploy.
- Documented current network issue: Nextcloud was unreachable from both the VPS and the Windows workstation, so WebDAV-dependent bot features cannot work until VM/network/public route is restored.

## 2026-06-16

- Deleted local Windows source files from `E:\Codex\NextCloud\gifs` only after verifying the same relative paths existed in Nextcloud `/gifs`.
- Removed `49133` local files and freed about `117.06G` on the Windows `E:` drive.

## 2026-06-15

- Transferred a new completed GIF batch from `E:\Codex\NextCloud\gifs` into Nextcloud `/gifs`.
- Excluded active `.part` files from transfer.
- Used server-side staging under `/mnt/nextcloud-extra/nextcloud-data/gifs-upload-20260615`.
- Verified `49133` local ready files matched staging with `missing_after_final=0`.
- Synced staging into `/opt/nextcloud/data/pustelga/files/gifs` with `rsync --chown=www-data:www-data`.
- Ran `occ files:scan --path='pustelga/files/gifs'`; scan completed with `Errors 0`.
- Verified final `/gifs` state: `111801` files, about `282G`.
- Removed staging and ran `fstrim`.

## 2026-06-08

- Transferred `7890` local GIF files from `E:\Codex\NextCloud\gifs` into Nextcloud `/gifs`.
- Verified `missing=0` before cleanup.
- Ran `occ files:scan --path='pustelga/files/gifs'`; scan completed with `Errors 0`.
- Verified WebDAV `/gifs` returned HTTP `207`.
- Removed local source folder after successful transfer, freeing about `46.31G`.

## 2026-06-05

- Repaired and verified the Windows Joplin bridge.
- Added a Startup-folder shortcut for the Joplin bridge autostart.
- Confirmed Joplin API responds with `JoplinClipperServer`.
- Ran a bridge self-test from Nextcloud queue to Joplin.

## 2026-06-04

- Reinstalled Joplin Desktop `3.6.14` after a broken installation was missing Electron runtime data.
- Updated the Joplin bridge runner to use an absolute bundled Python path instead of relying on `python` in `PATH`.
- Moved `/gifs` onto the 2 TB disk using a bind mount.
- Verified `/gifs/anihen-page-gif-downloader` transfer and scan.
- Removed temporary server archive/staging after verification and ran `fstrim`.

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
