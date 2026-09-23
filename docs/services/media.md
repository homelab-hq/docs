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
| **Bazarr** | Auto-downloads subtitle sidecars for Sonarr/Radarr content. |
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
4. **📲 Telegram Notification**: Sends a success or failure notification via Apprise → Telegram.

### Manual Cleanup

To validate all imported media and clean up completed torrents in bulk:

```bash
docker exec sonarr python3 /app/post_import.py --cleanup
```

## 💬 Subtitles (Bazarr)

**Bazarr** integrates with Sonarr/Radarr and automatically downloads subtitle
**sidecar `.srt` files** (e.g. `Movie (2024).en.srt`) next to each video, for the
whole library and every new download.

This delivery method is deliberate: Jellyfin serves sidecar SRT as a **soft,
external subtitle stream** that Apple clients render themselves — so the video
**keeps direct-playing** (see the hvc1 note below). Subtitles that must be
*burned in* (image-based PGS/VobSub, or heavily-styled ASS) would force a full
transcode and reintroduce playback stalls, so SRT sidecars are strongly
preferred.

- Media is mounted at `/media` (same path as Sonarr/Radarr) so Bazarr writes
  sidecars exactly where the videos live.
- Connect Bazarr to Sonarr (`http://sonarr:8989`) and Radarr
  (`http://radarr:7878`) with their API keys in the Bazarr web UI.

!!! note "Apple Direct-Play & Subtitle Format"
    Our HEVC library is tagged `hvc1` so iPads **direct-play** without a remux.
    Keeping subtitles as external SRT preserves that — a burn-in subtitle would
    undo it. Prefer SRT; avoid forcing PGS/ASS burn-in for casual viewing.

## 🔒 Network Security

No ports are exposed on the host for Sonarr, Radarr, Prowlarr, FlareSolverr, or the qBittorrent WebUI. All access is through Nginx Proxy Manager. Only qBittorrent's torrent port (6882) is exposed for peer connections.
