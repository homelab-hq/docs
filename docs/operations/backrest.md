# Backups (Backrest)

**Backrest** is a web UI and orchestrator for [Restic](https://restic.net/), managing encrypted, deduplicated, incremental backups of the homelab to off-site object storage.

## 💾 Role

- **Encrypted Backups**: Restic encrypts every snapshot client-side before upload — the provider only ever sees ciphertext.
- **Two Tiers**: A lean **config repo** on Cloudflare R2, and a separate **photo-archive repo** on Backblaze B2.
- **Scheduling & Retention**: Backrest handles schedules, retention/pruning, and integrity checks from one UI.
- **Access**: [backrest.ts.debdut.in](http://backrest.ts.debdut.in)

## 🧭 Guiding Principle

> Backrest exists to **rebuild the homelab** — configuration and small state, bounded in size. **Not bulk media.**

Jellyfin movies are never backed up (huge and re-acquirable). The same logic applies to Immich's photo/video *originals* — but unlike movies, personal photos are **irreplaceable**, so they get their own purpose-built cheap tier rather than being dumped into the config repo.

## 🗄️ The Two Repos

| Repo | Target | Contents | Plan schedule |
| :--- | :--- | :--- | :--- |
| **config** | Cloudflare **R2** | Whole `/userdata` config tree + Immich DB dumps | `30 2 * * *` |
| **immich-photos-b2** | Backblaze **B2** | Immich photo/video originals (`upload/library`) | `0 3 * * *` |

R2 is billed per-GB with **free egress** (handy for restoring configs); B2 is the cheapest restic-friendly bulk tier (~\$6/TB) for the growing photo library. The config repo is kept **bounded (~10 GB)** by aggressive excludes; the photo repo scales with the library.

## 📦 What Gets Backed Up (config repo)

The homelab repository is mounted **read-only** at `/userdata`:

```
/Volumes/homelab/services/homelab  →  /userdata (ro)
```

Backing up the config tree means the whole stack reconstructs from a snapshot plus the secrets in `.envrc`. Deliberately **excluded** to keep it lean:

- Logs, caches, `__pycache__`, `*.tmp`, NPM certs
- Immich: `data/postgres` (live DB — unsafe hot-copy), `upload/thumbs`, `upload/encoded-video`, `upload/upload`, `upload/library` (originals → B2 instead), and `import/` (the transient Takeout zip)
- AdGuard `data/data` (query logs — churny, regeneratable)
- Actual importer message/SMS archives (`*.zip`)

Immich's own nightly **DB dump** (`upload/backups/*.sql.gz`) *is* kept — that's the small, critical curation (albums, metadata, users).

## 🛠️ Configuration

| Setting | Value |
| :--- | :--- |
| **Internal Port** | 9898 |
| **Config repo** | Cloudflare R2 — creds `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` |
| **Photo repo** | Backblaze B2 — creds `B2_KEY_ID` / `B2_APP_KEY` |
| **Encryption** | `RESTIC_PASSWORD` (R2) · `B2_RESTIC_PASSWORD` (B2) — separate keys |
| **Retention (R2)** | 7 daily · 3 weekly · 2 monthly |
| **Retention (B2)** | 7 daily · 5 weekly · 12 monthly · 3 yearly |

## 🔒 Security

- Source data is mounted **read-only** — Backrest cannot modify what it backs up.
- Restic encryption means both providers only ever see ciphertext (a zip that lands in the repo is stored as encrypted pack files, never a readable file).
- `cap_drop: ALL`, `no-new-privileges`, 256 MB cap; reached only via Nginx Proxy Manager over Tailscale.

!!! warning "Guard the Restic Passwords"
    `RESTIC_PASSWORD` (R2) and `B2_RESTIC_PASSWORD` (B2) are the **only** keys to their backups. Lose one and that repo is unrecoverable — the provider cannot help. Keep both stored independently of the homelab (password manager + offline copy).

!!! note "Reclaiming space"
    Because restic deduplicates, an accidentally-backed-up large file isn't removed by simply excluding it going forward — it lingers in existing snapshots until they age out. To reclaim immediately: `restic forget <snapshot> --prune`, or `restic rewrite --exclude <path> --forget` to strip a path from *all* snapshots, then `prune`.
