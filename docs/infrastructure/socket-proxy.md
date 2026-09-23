# Docker Socket Proxy

Several services need to read the Docker API — Homepage for auto-discovery, Diun for image polling, Autoheal for restarts. Exposing the raw Docker socket to any of them would be equivalent to granting **host root**. Instead, each consumer talks to its own hardened [socket proxy](https://github.com/Tecnativa/docker-socket-proxy) with a minimal, per-consumer endpoint allowlist.

## 🛡️ Principle

> One proxy per consumer, each with the least privilege it needs.

If any single container is compromised, the blast radius is limited to that proxy's permitted endpoints — an attacker cannot pivot to host root through the Docker socket.

## 🔌 Proxies & Privileges

| Proxy | Consumer | Allowed | Write Access |
| :--- | :--- | :--- | :--- |
| `socket-proxy-homepage` | Homepage | `CONTAINERS`, `INFO`, `PING`, `VERSION` | ❌ Read-only |
| `socket-proxy-sage` | SAGE | `CONTAINERS`, `INFO`, `PING`, `VERSION` | ❌ Read-only |
| `socket-proxy-diun` | Diun | `CONTAINERS`, `IMAGES`, `INFO`, `PING`, `VERSION` | ❌ Read-only |
| `socket-proxy-xyops` | xyOps | `CONTAINERS`, `EXEC`, `IMAGES` | ⚠️ `POST` (exec) |
| `socket-proxy-autoheal` | Autoheal | `CONTAINERS`, `ALLOW_RESTARTS` | ⚠️ `POST` (restart) |

## ⚠️ Write-Access Trade-off

The two proxies that need write access (`xyops` for `docker exec`, `autoheal` for container restart) require `POST=1`. Due to how the Tecnativa proxy evaluates rules — a blanket `deny unless METH_GET || env(POST)` fires **before** granular allow rules — enabling `POST` also opens the generic `/containers` create/destroy path.

This is a documented, accepted trade-off: both consumers are **internal-only**, unexposed, and run trusted images. Every proxy still:

- Mounts the Docker socket **read-only** (`/var/run/docker.sock:ro`)
- Drops all capabilities (`cap_drop: ALL`, keeping only `SETUID`/`SETGID` for the proxy's own privilege drop)
- Runs with `no-new-privileges` and a 32 MB memory cap

## 🔒 Why It Matters

This pattern is the practical implementation of the **Least Privilege** principle from the [Security Model](../security.md): no application container ever holds a raw Docker socket, and each one's Docker reach is auditable at a glance from its proxy's environment block.
