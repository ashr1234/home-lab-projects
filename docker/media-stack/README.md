# 🎬 Media Stack

**Purpose:**  
Media server and automation tools for movies, TV, and music.  

**Components:**  
- **Jellyfin** → media streaming (DLNA + web clients).  
- **Navidrome** → music server (Subsonic API).  
- **qBittorrentVPN** → client protected with PIA VPN.  
- **Radarr / Sonarr / Lidarr** → organize and index movies, TV, and music.  
- **Prowlarr** → indexer manager.  

**Networking:**  
- Jellyfin in host mode for DLNA/Kodi integration.  
- qBittorrent fully isolated behind VPN container.  

**Security:**  
- VPN credentials redacted in compose file.  
- Access proxied through Caddy.  
