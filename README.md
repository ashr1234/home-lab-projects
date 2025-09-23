# 🏠 Homelab Projects – Ashil Muhammod Rafique

Welcome 👋  
This repository documents my self-hosted **networking and security lab**, built on **Proxmox VE** with a mix of LXCs and Dockerized services.  

My focus is **Network Security Engineering** — combining network design (VLANs, VPN, firewalling) with secure service deployment, automation, and backup strategies.

---

## ⚙️ Homelab Infrastructure

**Base Hypervisor**  
- **Proxmox VE 8.4** on Lenovo ThinkCentre M73.  
- Designed with **separation of concerns**: LXCs for networking, VM for applications.  

**Virtual Machines & Containers**
- **LXC 100 – Pi-hole** → DNS resolution + ad blocking.  
- **LXC 101 – WireGuard VPN** → secure remote access (10.8.0.0/24).  
- **VM 102 – Docker Host** → runs container stack (media, productivity, security, monitoring).  

**Startup Order**  
- Boot priority configured to ensure **DNS → VPN → Apps** always start in correct order.  

**Resource Management**  
- RAM allocations locked per VM/LXC.  
- CPU limits + pinning prevent service starvation.  

---

## 🔐 Network Security Projects

### VLAN Segmentation
- **192.168.1.0/24 – LAN** → Trusted: servers, workstations, Proxmox, Docker.  
- **192.168.5.0/24 – Guest** → Guest Wi-Fi devices (internet-only).  
- **192.168.10.0/24 – IoT** → Smart devices (TVs, plugs, sensors).  
- Firewall rules:  
  - **Guest VLAN** → blocked from LAN/IoT, internet-only.  
  - **IoT VLAN** → permitted only limited outbound to internet (firmware updates, vendor APIs).  
  - **LAN VLAN** → can manage IoT devices if required.  
- Prevents **lateral movement** and **IoT compromise** from affecting critical LAN resources.  

### WireGuard VPN + DDNS
- **WireGuard server** in Proxmox LXC.  
- **Client subnet**: 10.8.0.0/24.  
- **Split tunneling** → clients route only 192.168.1.0/24 and 192.168.10.0/24 over VPN.  
- **DDNS integration**: dynamic ISP IPs updated via `ashil.uk`.  
- **Security**: ChaCha20 encryption, Curve25519 keys, NAT rules with least privilege.  

---

## 🛡️ Security Services

- **Vaultwarden** → self-hosted password manager (TLS enforced with Caddy).  
- **Caddy Reverse Proxy** → central TLS gateway for all container services (`*.docker.lan`).  
- **TLS Everywhere** → local CA with automatic cert renewal.  
- **Port restrictions** → containers not exposed directly, only via Caddy.  

---

## 🗄️ Containerized Applications (VM 102)

- **Media & Indexing** → Jellyfin, Radarr, Sonarr, Lidarr, Prowlarr, qBittorrentVPN.  
- **Productivity** → Nextcloud (MariaDB + Redis), FreshRSS.  
- **Finance** → Firefly III + Importer.  
- **Security & Management** → Vaultwarden, Portainer, Filebrowser, Homepage.  
- **Automation** → Home Assistant (controls IoT smart plugs, sensors).  
- **Monitoring** → Glances.  

📂 Full per-container setup docs available in [`/docker`](./docker).  

---

## 💾 Backup & Disaster Recovery

### Current
- **Proxmox vzdump** → scheduled VM/LXC snapshots.  
- **Restic** → encrypted, deduplicated backups of:  
  - `/etc/pihole/` (DNS configs).  
  - `/etc/wireguard/` (VPN configs).  
  - `/docker/configs/` (container configs).  
  - `/mnt/hdd` (media, Nextcloud, Firefly III data).  
- **Rsync** → external HDD for bulk data.  

