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

## Verification

The WebDAV endpoint was checked with a dedicated app password:

```text
HTTPS /status.php: 200
WebDAV PROPFIND /Joplin: 207
WebDAV PUT test file: 201
WebDAV DELETE test file: 204
```

## Credentials

Use a dedicated Nextcloud app password for Joplin. Do not use the main account password.

The current local-only settings file is on the operator workstation:

```text
E:\Codex\NextCloud\joplin-webdav-settings.txt
```

That file is intentionally not committed because it contains the app password.

Nextcloud auth token ID for the current Joplin app password:

```text
26
```

To revoke it:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ user:auth-tokens:delete pustelga 26
```

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

## Test

Create a note named:

```text
Тестовая заметка Joplin
```

Check:

- it appears on Desktop;
- it appears on mobile;
- files appear in Nextcloud `/Joplin`;
- edits sync in both directions.

## Backup

Recommended backup set:

- Nextcloud data directory;
- Nextcloud database;
- weekly Joplin `.jex` export from Desktop.

Joplin `.jex` export is separate from WebDAV sync and is useful for clean application-level restore.

