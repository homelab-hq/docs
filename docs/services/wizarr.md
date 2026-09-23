# Media Invitations (Wizarr)

**Wizarr** is a user invitation and onboarding system for the media stack. Instead of manually creating accounts, you send a single invite link that automatically provisions the user on the media server and guides them through installing apps and accessing request systems.

## 🎟️ Role

- **Invite Links**: Generate time-limited, optionally single-use invite links to share with trusted users.
- **Automated Provisioning**: Accepted invites create the user account directly on the target media server.
- **Guided Onboarding**: New users are walked through app downloads and how to reach the request system ([Seerr](seerr.md)).
- **Multi-Server**: Supports Jellyfin, Plex, Emby, and — relevant here — **Audiobookshelf** and **Kavita**, so one flow can onboard across services.
- **Access**: [wizarr.ts.debdut.in](http://wizarr.ts.debdut.in)

## 🔗 Why It Fits

[Jellyfin](media.md) is published to trusted users over the [public Cloudflare Tunnel](../infrastructure/cloudflared.md). Wizarr turns "create an account and explain the setup" into a self-service link, which is exactly what a shared-but-private instance needs.

!!! warning "Invited Users Access Jellyfin via Browser Only"
    The public Jellyfin endpoint sits behind [Cloudflare Access (Google SSO)](../infrastructure/cloudflared.md), which only works in a **web browser** — native Jellyfin apps cannot authenticate through it. Invited users must (a) be on the Cloudflare Access allowlist and (b) use the browser at `jellyfin.debdut.in`. This is an accepted trade-off; the SSO gate is kept in place deliberately.

## 🛠️ Configuration

| Setting | Value |
| :--- | :--- |
| **Internal Port** | 5690 |
| **Data** | `./data` (self-contained SQLite — no external database) |
| **Run As** | PUID 501 / PGID 20 (matches host ownership) |

Media-server connection details (Jellyfin URL + API key) are entered in Wizarr's first-run web setup, so no secrets are required in `.envrc`.

## 🔓 Passwordless Access

Wizarr's built-in login is disabled (`DISABLE_BUILTIN_AUTH=true`) — the `/login` route auto-authenticates and there is no recurring password prompt. The **Tailscale network is the trust boundary**: only devices on the tailnet can reach `wizarr.ts.debdut.in`, and any such device has full admin access.

!!! note "One-Time Setup Is Still Required"
    `DISABLE_BUILTIN_AUTH` only skips the *recurring login*, not the first-run setup. Wizarr requires exactly one `AdminAccount` to exist, so on first launch you must complete the onboarding wizard once and create an admin username/password. Those credentials never need to be entered again — every later visit auto-logs-in. There is no supported way to skip this one-time step.

!!! warning "Do Not Expose Publicly"
    With built-in auth disabled and no upstream auth provider, this configuration is **only safe while the service stays tailnet-only**. Never route Wizarr through the public [Cloudflare Tunnel](../infrastructure/cloudflared.md) in this state — doing so would leave the admin UI open to the internet. Publishing it publicly would require fronting it with Cloudflare Access (Google SSO) plus a bypass for the invite paths (`/join`, `/j`, `/static`, `/setup`, `/wizard`, `/image-proxy`, `/cinema-posters`).

## 🔒 Security

- No host ports exposed; reached only through Nginx Proxy Manager over Tailscale.
- `cap_drop: ALL` with a minimal add set for privilege drop and file ownership; `no-new-privileges`; 256 MB cap.
- The `./data` directory sits under the homelab repo, so it is included in [Backrest](../operations/backrest.md) off-site backups automatically.

!!! note "Invite Links Are Public"
    An invite link lets its holder create an account on your media server. Prefer single-use, expiring invites, and revoke unused ones from the Wizarr admin UI.
