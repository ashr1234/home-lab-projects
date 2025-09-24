# 💾 Restic Backup Strategy

## 🔹 Purpose
Backups are critical for protecting homelab data and configurations.  
This document outlines the intended backup strategy using **[Restic](https://restic.net/)** — a modern, secure, and efficient backup tool.

---

## 🔹 Backup Targets
Planned Restic backups will cover:

- **Proxmox VM/LXC configs**  
  - Backups of `/etc/pve` and VM definitions.  

- **Docker stack configurations**  
  - `/docker` folder with all `docker-compose.yml` and configs.  
  - Excludes secrets (stored in `.env` files separately).  

- **Application data**  
  - Nextcloud data volume (`./nextcloud_storage`).  
  - Vaultwarden `/data`.  
  - Firefly III uploads and database dump.  
  - Navidrome and Jellyfin configs.  

---

## 🔹 Storage Backends
Restic supports multiple backends. For flexibility, backups can be pushed to:

- **Local NAS** → `/mnt/nas/backups/` (fast restore).  
- **External USB HDD** (cold/offline storage).  
- **Cloud object storage (optional)**  
  - Backblaze B2  
  - AWS S3  
  - Wasabi  

---

## 🔹 Example Backup Script
Example `restic-backup.sh` to back up Docker configs and important volumes:

```bash
#!/bin/bash
export RESTIC_REPOSITORY=/mnt/nas/backups/restic
export RESTIC_PASSWORD_FILE=/root/.restic-pass

# Init repository (first run only)
# restic init

# Backup Proxmox configs
restic backup /etc/pve

# Backup Docker stack configs
restic backup /home/docker/

# Backup Nextcloud data
restic backup /mnt/storage/nextcloud_storage

# Forget old snapshots (keep last 7 daily, 4 weekly, 6 monthly)
restic forget --prune --keep-daily 7 --keep-weekly 4 --keep-monthly 6
