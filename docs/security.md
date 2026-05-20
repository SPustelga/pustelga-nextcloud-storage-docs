# Security

## Repository Policy

This repository must not contain:

- SSH passwords
- Nextcloud user passwords
- Nextcloud app passwords
- qBittorrent passwords
- QR login images
- database dumps
- private notes or file contents
- copied logs with secrets

The `.gitignore` blocks common secret filenames, but still review `git diff --staged` before every push.

## Local Secret Locations

Secrets currently live outside this repo:

```text
E:\Codex\NextCloud\nextcloud-mobile-app-password.txt
E:\Codex\NextCloud\nextcloud-login-qr.png
E:\Codex\NextCloud\qbittorrent-webui-password.txt
E:\Codex\NextCloud\server-config-backup-20260512-073827
```

## Token Hygiene

If an app password is pasted into chat, a note, a screenshot, or a repo:

1. Revoke it immediately.
2. Create a new app password.
3. Update the local QR/text file.
4. Remove and re-add the affected mobile account.
5. Check logs for repeated `401` and reset bruteforce attempts if needed.

## Public Repo Warning

If this documentation is pushed to a public GitHub repository, keep it sanitized. For a private repo, the same rule still applies: secrets should stay in a password manager or local encrypted backup, not in git history.

