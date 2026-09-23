# Homelab Assistant (SAGE)

**SAGE** is a custom Telegram bot that acts as a conversational control plane for the homelab, backed by a **local LLM** (Ollama) so no queries leave the network.

## 🤖 Role

- **Conversational Ops**: Ask about homelab status and issue commands from Telegram.
- **Local Inference**: Uses **Ollama** (`qwen2.5:3b`) running on the host — no cloud AI calls.
- **Service Control**: Integrates with Sonarr, Radarr, and AdGuard to answer queries and take actions.
- **Health & Disk**: Reports container health and disk usage across mounted paths.

## 🔗 Integrations

| System | Access |
| :--- | :--- |
| **Ollama** | `host.docker.internal:11434` (local model inference) |
| **Sonarr / Radarr** | API keys for media queries |
| **AdGuard** | Credentials for DNS stats/control |
| **Docker** | Read-only via [`socket-proxy-sage`](../infrastructure/socket-proxy.md) |
| **Telegram** | Bot token; restricted to an allowlisted chat ID |

## 🔒 Security

- **Access Control**: Only the allowlisted `SAGE_ALLOWED_USERS` chat ID may interact with the bot.
- **Least Privilege**: Docker visibility is read-only through a dedicated socket proxy; the data mount is read-only (`./mount:/data:ro`).
- `cap_drop: ALL`, `no-new-privileges`, 128 MB cap.

!!! note "Custom Build"
    Unlike most services, SAGE is built from a local `Dockerfile` (`build: .`) rather than a published image.
