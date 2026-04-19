# Media & Streaming

The media stack is designed for seamless, high-performance streaming across the Tailscale network.

## 🎞️ Jellyfin

**Jellyfin** is the centerpiece of the media stack, providing an open-source, private alternative to Plex or Emby.

- **Storage**: Media is mounted from a dedicated storage volume (`/Volumes/homelab/streaming/media`).
- **Transcoding**: Configured to use hardware acceleration (where available) to support multiple remote streams.
- **Client Support**: Fully accessible via Jellyfin apps on iOS, Android, and Smart TVs over the Tailscale tunnel.
- **Access**: [jellyfin.ts.debdut.in](http://jellyfin.ts.debdut.in)

## 🎬 Servarr Stack

The **Servarr** ecosystem automates the entire media lifecycle.

| Service | Role |
| :--- | :--- |
| **Sonarr** | Automated TV show management and tracking. |
| **Radarr** | Automated movie management and tracking. |
| **Prowlarr** | Centralized indexer manager for Sonarr and Radarr. |
| **qBittorrent** | High-performance BitTorrent client with WebUI. |
| **Recyclarr** | Syncs quality profiles from TRaSH Guides. |
| **FlareSolverr** | Cloudflare bypass proxy for Prowlarr indexers. |
| **Autoheal** | Automatically restarts unhealthy containers. |

## ⚙️ Post-Import Pipeline

A custom `post_import.py` script runs automatically after Sonarr/Radarr imports media:

1. **Conversion**: Remuxes MKV to MP4. Incompatible audio codecs (DTS, Opus) are transcoded to AAC stereo for universal device support.
2. **🍎 iPad Validation**: Verifies the converted file has compatible codecs (H.264/H.265 video, AAC audio) and passes an integrity check.
3. **🧹 Torrent Cleanup**: On successful validation, removes the torrent and download files from qBittorrent via API.
4. **📲 Telegram Notification**: Sends a success or failure notification via Bridge → Apprise → Telegram.

### Manual Cleanup

To validate all imported media and clean up completed torrents in bulk:

```bash
docker exec sonarr python3 /app/post_import.py --cleanup
```

## 🔒 Network Security

No ports are exposed on the host for Sonarr, Radarr, Prowlarr, FlareSolverr, or the qBittorrent WebUI. All access is through Nginx Proxy Manager. Only qBittorrent's torrent port (6882) is exposed for peer connections.
