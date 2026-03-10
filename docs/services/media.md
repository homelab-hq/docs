# Media & Streaming

The media stack is designed for seamless, high-performance streaming across the local and Tailscale networks.

## 🎞️ Jellyfin

**Jellyfin** is the centerpiece of the media stack, providing an open-source, private alternative to Plex or Emby.

- **Storage**: Media is mounted from a dedicated, secure storage volume.
- **Transcoding**: Configured to use hardware acceleration (where available) to support multiple remote streams.
- **Client Support**: Fully accessible via standard Jellyfin apps on iOS, Android, and Smart TVs over the Tailscale tunnel.

## 🎬 Servarr Stack

The **Servarr** ecosystem automates the entire media lifecycle.

| Service | Role |
| :--- | :--- |
| **Sonarr** | Automated TV show management and tracking. |
| **Radarr** | Automated movie management and tracking. |
| **Prowlarr** | Centralized indexer manager for Sonarr and Radarr. |
| **qBittorrent** | High-performance BitTorrent client with WebUI. |

## ⚙️ Post-Processing

A custom utility handles the remuxing of downloaded media:

- **Remuxing**: Losslessly converts MKV to MP4 (copying codecs).
- **Automation**: Triggered via post-import scripts to ensure maximum compatibility with streaming clients.
- **Notification**: Alerts the user when new media is ready for playback.
