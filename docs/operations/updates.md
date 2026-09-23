# Updates & Scheduling

Two services keep the stack current and automate recurring maintenance: **Diun** watches for new container images, and **xyOps** runs scheduled jobs against the homelab.

## 🔔 Diun — Image Update Notifier

[Diun](https://crazymax.dev/diun/) polls container registries and notifies when a newer image is available for any running container.

- **Discovery**: Reads running containers via a read-only [`socket-proxy-diun`](../infrastructure/socket-proxy.md) — no raw Docker socket access.
- **Notifications**: New-image alerts are delivered through the notification pipeline to Telegram.
- **Config**: Watch rules live in `diun.yml` (mounted read-only).

!!! note "Notify, Don't Auto-Update"
    Diun only *reports* available updates. Applying them is a deliberate step via `make update`, keeping upgrades under manual control.

## ⏰ xyOps — Job Scheduler

[xyOps](https://github.com/pixlcore) (with its `xysat` satellite agent) provides scheduled task orchestration for the homelab.

| Container | Role |
| :--- | :--- |
| `xyops` | Scheduler UI and job engine |
| `xysat` | Satellite agent that executes jobs |

- **Docker Exec**: Jobs that operate on containers reach Docker through [`socket-proxy-xyops`](../infrastructure/socket-proxy.md), which permits `exec` (see the write-access trade-off on that page).
- **Access**: [xyops.ts.debdut.in](http://xyops.ts.debdut.in)

## 🩹 Autoheal

Complementing scheduled maintenance, **Autoheal** watches container healthchecks and restarts any container that reports unhealthy — via a dedicated `socket-proxy-autoheal` limited to the restart endpoint. Containers opt in with the `autoheal=true` label.
