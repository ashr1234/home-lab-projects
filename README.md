🏠 Homelab Projects – Ashil Muhammod Rafique

Welcome 👋
This repository documents my self-hosted networking and security lab, built on Proxmox VE with LXCs and Dockerized services.

My focus is Network Security Engineering — combining network design (VLANs, VPN, firewalling) with secure service deployment, automation, and backup strategies.

⚙️ Homelab Infrastructure

Base Hypervisor

Proxmox VE 8.4 running on Lenovo ThinkCentre M73.

Services split into LXCs (network/security) and VM (application stack).

Virtual Machines & Containers

LXC 100 – Pi-hole → DNS resolution + ad blocking.

LXC 101 – WireGuard VPN → secure remote access (10.8.0.0/24).

VM 200 – OPNsense Router → dual-NIC router/firewall handling VLANs.

VM 102 – Docker Host → runs container stack (media, productivity, security, monitoring).

Resource Management

Startup sequencing: DNS → VPN → Router → Apps.

Memory locked per VM/LXC.

CPU pinning to prevent starvation.

🔐 Network Security Projects
OPNsense Router & VLAN Segmentation

I virtualized my router by running OPNsense in Proxmox, with two NICs:

NIC1 (built-in) → connected to ISP router, used as WAN uplink.

NIC2 (USB Ethernet) → connected to ISP router, but used for LAN/AP access.

Both NICs are passed through to the OPNsense VM.

The ISP router (Vodafone PowerHub) is now only an access point:

Provides Wi-Fi and LAN switching.

DHCP and NAT disabled.

All traffic passes through OPNsense.

VLANs in OPNsense:

192.168.1.0/24 – LAN (Proxmox, Docker host, trusted clients).

192.168.5.0/24 – Guest (Wi-Fi guests, internet-only).

192.168.10.0/24 – IoT (smart devices, outbound restricted).

Firewall Rules:

Guest → Internet ✅ | Guest → LAN/IoT ❌

IoT → Internet (restricted ports) ✅ | IoT → LAN ❌ (except HA controls)

LAN → all VLANs ✅ (for management).

WireGuard VPN + DDNS

WireGuard LXC provides secure remote access.

Clients join 10.8.0.0/24 and can access LAN + IoT VLANs.

DDNS integration: Proxmox updates WAN IP to provider on ISP change.

Encryption: ChaCha20 + Curve25519.

🛡️ Security Services

Vaultwarden → self-hosted password manager, behind Caddy TLS.

Caddy Reverse Proxy → central TLS gateway for all container services (*.docker.lan).

TLS Everywhere → internal CA with auto cert renewal.

Port restrictions → containers only exposed internally, proxied via Caddy.

🗄️ Containerized Applications (VM 102 – Docker Host)

Media & Indexing → Jellyfin, Radarr, Sonarr, Lidarr, Prowlarr, qBittorrentVPN.

Productivity → Nextcloud (MariaDB + Redis), FreshRSS.

Finance → Firefly III + Importer.

Security & Management → Vaultwarden, Portainer, Filebrowser, Homepage.

Automation → Home Assistant (humidity-controlled smart plugs, sensors).

Monitoring → Glances.

📂 Full per-container setup docs are available in /docker
.

💾 Backup & Disaster Recovery

Proxmox vzdump → scheduled VM/LXC snapshots.

Restic → encrypted, deduplicated backups of:

/etc/pihole/ (DNS configs).

/etc/wireguard/ (VPN configs).

/docker/configs/ (container configs).

/mnt/hdd (media, Nextcloud, Firefly data).

Rsync → to external HDD for bulk data.

Verification:

restic check weekly.

Monthly test restores.

📂 Details in /backups/restic.md
.

🚀 Future Improvements

Networking & Security

Add IDS/IPS (Suricata/Zeek) in OPNsense.

Deploy pfBlockerNG for threat intelligence feeds.

Expand IoT VLAN with strict egress rules.

Internal PKI for cert management.

Infrastructure

Automate provisioning with Ansible/Terraform.

Add Proxmox HA node.

TrueNAS Scale with ZFS for storage.

Offsite backups with restic + rclone (Backblaze/Wasabi).

Observability

Prometheus + Grafana dashboards.

Uptime Kuma for service monitoring.

Centralized logs (ELK or Loki stack).
