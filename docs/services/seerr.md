# Media Requests (Seerr)

**Seerr** is the request-and-discovery front-end for the media library — a self-hosted way for trusted users to browse, request, and track movies and TV shows that automatically feed into the [Servarr stack](media.md).

## 🎯 Role

- **Discovery**: Browse trending and popular titles backed by TMDB metadata.
- **Requests**: Users request a movie or show; approved requests are handed to **Radarr** / **Sonarr** for automated fetching.
- **Tracking**: Shows request status (pending, processing, available) and notifies users when content is ready.
- **Access**: [seerr.ts.debdut.in](http://seerr.ts.debdut.in)

## 🔗 Integrations

| System | Purpose |
| :--- | :--- |
| **Jellyfin** | User authentication and library sync |
| **Radarr / Sonarr** | Fulfilment of approved requests |
| **TMDB** | Metadata, artwork, and discovery |

## 🔒 Security

- No host ports exposed; reached only through Nginx Proxy Manager over Tailscale.
- Runs with `cap_drop: ALL` and `no-new-privileges`, capped at 512 MB.

!!! note "TMDB Connectivity"
    TMDB metadata lookups can be sensitive to upstream network peering. If discovery pages fail to load, check the egress path before assuming a Seerr fault.
