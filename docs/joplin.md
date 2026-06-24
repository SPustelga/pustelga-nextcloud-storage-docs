# Joplin WebDAV Sync

## Goal

Use Joplin on Windows, Android, and iOS with the existing Nextcloud instance as the sync backend.

Joplin Server is intentionally not deployed for the first stage. Sync uses Nextcloud WebDAV only.

## Nextcloud Side

Server:

```text
https://nextcloud.pustelga.xyz
```

Nextcloud user:

```text
pustelga
```

Joplin sync folder:

```text
/Joplin
```

WebDAV URL:

```text
https://nextcloud.pustelga.xyz/remote.php/dav/files/pustelga/Joplin
```

The folder was created and scanned in Nextcloud.

## Credentials

Use a dedicated Nextcloud app password for Joplin. Do not use the main account password.

The current local-only settings file is on the operator workstation:

```text
E:\Codex\NextCloud\joplin-webdav-settings.txt
```

That file is intentionally not committed because it contains the app password.

## Joplin Desktop

Open:

```text
Tools -> Options -> Synchronisation
```

Use:

```text
Synchronisation target: Nextcloud
Nextcloud WebDAV URL: https://nextcloud.pustelga.xyz/remote.php/dav/files/pustelga/Joplin
Username: pustelga
Password: dedicated Joplin app password
```

Then click:

```text
Check synchronisation configuration
```

## Joplin Mobile

On Android or iOS:

```text
Configuration -> Synchronisation
Synchronisation target: Nextcloud
Nextcloud WebDAV URL: https://nextcloud.pustelga.xyz/remote.php/dav/files/pustelga/Joplin
Username: pustelga
Password: dedicated Joplin app password
```

Then run sync.

## Encryption

Enable encryption from Joplin Desktop first:

```text
Settings -> Encryption -> Enable encryption
```

Set a Joplin master password and let Desktop fully sync. Then sync mobile and enter the same master password when Joplin asks for it.

Important: the Joplin master password is separate from the Nextcloud app password.

## Telegram To Joplin Bridge

Telegram text notes and todo captures are queued in Nextcloud and then imported into local Joplin Desktop through the Joplin Web Clipper API.

Queue folders:

```text
/Telegram Joplin Queue/pending/*.json
/Telegram Joplin Queue/done/*.json
/Telegram Joplin Queue/failed/*.json
```

Local bridge files:

```text
E:\Codex\NextCloud\joplin_nextcloud_bridge.py
E:\Codex\NextCloud\run-joplin-nextcloud-bridge.ps1
E:\Codex\NextCloud\joplin-nextcloud-bridge.windows.log
```

Autostart:

```text
C:\Users\vodob\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\JoplinNextcloudBridge.lnk
```

The bridge uses the local Joplin API:

```text
http://127.0.0.1:41184
```

Health check:

```powershell
Invoke-WebRequest http://127.0.0.1:41184/ping -UseBasicParsing
```

Expected response:

```text
JoplinClipperServer
```

The bridge creates/imports items into the Joplin notebook:

```text
Telegram
```

Todo queue items are created with:

```text
is_todo = 1
```

## Bridge Repair Notes

On `2026-06-04`, Joplin Desktop on Windows was repaired because the installation was missing Electron runtime data (`icudtl.dat`) and would not start. It was reinstalled with:

```text
Joplin-Setup-3.6.14.exe
```

After reinstall:

```text
C:\Users\vodob\AppData\Local\Programs\Joplin\icudtl.dat
```

was present and `/ping` returned `JoplinClipperServer`.

The bridge script was also changed to use an absolute bundled Python path instead of relying on `python` in `PATH`.

## Verification

A bridge self-test on `2026-06-05` queued a JSON item and confirmed that the bridge created a Joplin note, then Joplin synchronized it to `/Joplin` in Nextcloud.

Useful checks:

```powershell
Get-Content E:\Codex\NextCloud\joplin-nextcloud-bridge.windows.log -Tail 80
Invoke-WebRequest http://127.0.0.1:41184/ping -UseBasicParsing
```

Queue check through WebDAV:

```text
/Telegram Joplin Queue/pending
/Telegram Joplin Queue/done
/Telegram Joplin Queue/failed
```

If `pending` is empty and recent items are in `done`, the bridge is working. If notes do not appear on mobile, troubleshoot Joplin mobile sync rather than the bridge.

## Backup

Recommended backup set:

- Nextcloud data directory;
- Nextcloud database;
- weekly Joplin `.jex` export from Desktop.

Joplin `.jex` export is separate from WebDAV sync and is useful for clean application-level restore.
