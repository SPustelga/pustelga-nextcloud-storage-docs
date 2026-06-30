# GIF Library

## Purpose

The Nextcloud folder `/gifs` stores the large GIF/video library used by the Telegram bot's random GIF button.

Physical path on the VM:

```text
/opt/nextcloud/data/pustelga/files/gifs
```

The folder is backed by the added 2 TB disk through a bind mount:

```text
/mnt/nextcloud-extra/nextcloud-data/pustelga-files/gifs
  -> /opt/nextcloud/data/pustelga/files/gifs
```

## Current State

Last verified after the `2026-06-30` transfer:

```text
Files: 175367
Size: 439G
Nextcloud scan: Errors 0
```

Disk state after staging cleanup:

```text
/dev/sdb1  2.0T  801G used  1.1T free  43%  /mnt/nextcloud-extra
```

Completed `2026-06-30` local batch:

```text
Local source: E:\Codex\NextCloud\gifs
Local files verified against final /gifs: 63566
Local source size: 168493159097 bytes
Final /gifs files after scan: 175367
Final /gifs size after scan: 439G
Nextcloud scan updated files: 64451
Nextcloud scan errors: 0
Deleted verified local files: 63566
Local files remaining: 0
Server staging removed: /mnt/nextcloud-extra/nextcloud-data/gifs-upload-20260630
fstrim: completed
```

## Local Source Folder

Transfers have used this Windows source folder:

```text
E:\Codex\NextCloud\gifs
```

The source folder may be actively written by download tools. In that case it can contain temporary `.part` files. Do not transfer `.part` files.

## Transfer Pattern

Recommended high-level flow:

1. Count local ready files, excluding `*.part`.
2. Transfer to a server-side staging folder on the 2 TB disk.
3. Verify local ready relative paths against staging relative paths.
4. `rsync` staging into the real Nextcloud `/gifs` folder with `www-data` ownership.
5. Run targeted `occ files:scan`.
6. Remove staging and run `fstrim`.
7. Only then delete the local source files that are confirmed present on the server.

Staging path used in June 2026:

```text
/mnt/nextcloud-extra/nextcloud-data/gifs-upload-YYYYMMDD
```

Example final move from staging into Nextcloud:

```bash
sudo rsync -a --chown=www-data:www-data \
  /mnt/nextcloud-extra/nextcloud-data/gifs-upload-YYYYMMDD/ \
  /opt/nextcloud/data/pustelga/files/gifs/
```

Scan after move:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ files:scan --path='pustelga/files/gifs'
```

Cleanup:

```bash
sudo rm -rf /mnt/nextcloud-extra/nextcloud-data/gifs-upload-YYYYMMDD
sudo fstrim -av
```

## Important Lessons

Large direct tar streams can be fragile when the local source folder is still being written. A prior transfer failed because tar hit an actively written `.part` file. Use an exclude or a generated file list of completed files only.

Good source filter:

```text
include finished media files
exclude *.part
```

When a partial transfer already exists in staging, do not blindly restart from zero if time matters. Compare relative path lists and send only missing files.

## Telegram Bot Behavior

The bot reads `/gifs` over Nextcloud WebDAV.

The folder structure is nested, for example:

```text
/gifs/fapreactor/porn-gif/page-3986/*.gif
/gifs/pornogifs-net-pages-1001-1200/*.gif
```

The bot must search recursively. The implementation now randomly walks nested folders up to a bounded depth and selects media files with these extensions:

```text
.gif
.mp4
.webm
.mov
```

If Nextcloud WebDAV is unreachable, the bot should report a connectivity error instead of saying the folder is empty.

## Deleting Local Sources

Before deleting local Windows files, verify the matching relative paths exist on the server. The `2026-06-16` cleanup removed only files that existed in `/gifs` on Nextcloud:

```text
Deleted local files: 49133
Freed: about 117.06G
Local folder removed because it became empty: E:\Codex\NextCloud\gifs
```

The `2026-06-30` cleanup followed the same rule. Local files were deleted only after every local relative path and size was confirmed present in final `/gifs` and after `occ files:scan --path='pustelga/files/gifs'` completed with `Errors 0`.
