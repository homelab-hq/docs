# Audiobooks & Podcasts (Audiobookshelf)

**Audiobookshelf** is a self-hosted server for audiobooks and podcasts, with progress sync across devices and native mobile apps that cache content for offline listening.

## 🎧 Role

- **Audiobooks**: Serves the library from `/Volumes/homelab/streaming/media/audiobooks`.
- **Podcasts**: Manages subscriptions and auto-downloads new episodes to `/Volumes/homelab/streaming/media/podcasts`.
- **Progress Sync**: Playback position follows you across web and mobile clients.
- **Offline**: Mobile apps download titles for listening without connectivity — well suited to a commute.
- **Access**: [audiobookshelf.ts.debdut.in](http://audiobookshelf.ts.debdut.in)

## 🛠️ Configuration

| Setting | Value |
| :--- | :--- |
| **Internal Port** | 80 |
| **Config / Metadata** | `./config`, `./metadata` |
| **Run As** | UID 501 / GID 20 (matches host media ownership) |

## 🔒 Security

- No host ports exposed; proxied through Nginx Proxy Manager over Tailscale.
- `cap_drop: ALL` with only the capabilities needed to bind port 80 and manage file ownership; `no-new-privileges`; 1 GB cap.
