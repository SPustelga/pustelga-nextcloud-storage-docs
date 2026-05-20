# Operations

## SSH

VM LAN address:

```text
192.168.1.42
```

Admin/operator user:

```text
sb
```

Do not store the SSH password in this repository.

## Docker Compose

```bash
cd /opt/nextcloud
sudo docker compose ps
sudo docker compose logs --tail=200 app
sudo docker compose logs --tail=200 qbittorrent
```

## Nextcloud OCC

Run OCC inside the app container as `www-data`:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ status
sudo docker compose exec -T -u www-data app php occ app:list
sudo docker compose exec -T -u www-data app php occ files_external:list --all
```

## Rescan Files

Torrent folder:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ files:scan --path='pustelga/files/Torrents'
```

External storage:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ files_external:scan 1 -vvv
```

## Brute Force / Rate Limit Reset

If mobile clients repeatedly use a bad token, Nextcloud can answer `429`.

Check/reset for the reverse proxy container IP:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ security:bruteforce:attempts 172.18.0.5
sudo docker compose exec -T -u www-data app php occ security:bruteforce:reset 172.18.0.5
```

## App Passwords

Create a new app password using the real Nextcloud login password through `NC_PASS`:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data -e NC_PASS='REDACTED' app php occ user:auth-tokens:add --password-from-env pustelga
```

List or revoke app tokens:

```bash
cd /opt/nextcloud
sudo docker compose exec -T -u www-data app php occ user:auth-tokens:list pustelga
sudo docker compose exec -T -u www-data app php occ user:auth-tokens:delete pustelga TOKEN_ID
```

