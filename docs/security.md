# Security Model

The security of this homelab is built on a **Zero-Trust Networking Architecture**. This ensures that we do not trust any network by default—even our local home network—and we rely on verified identity and encryption for all communications.

## 🛡️ Core Security Principles

1.  **No Open Inbound Ports**: No ports are forwarded on the internet-facing router. Inbound access arrives only through outbound-initiated tunnels — the Tailscale mesh for private services, and a Cloudflare Tunnel for the few publicly-published services.
2.  **Encrypted Mesh Networking**: Every node is part of a [Tailscale](https://tailscale.com) mesh network, providing end-to-end WireGuard® encryption.
3.  **Authenticated Access**: All access is identity-gated — private services via Tailscale's OIDC/SSO, and publicly-published services via [Cloudflare Access](https://www.cloudflare.com/zero-trust/products/access/) (Google OAuth SSO).
4.  **Least Privilege**: Each Docker container runs with limited CPU/Memory resources and restricted network access where possible. Containers that need Docker visibility reach it through a read-only **socket proxy** rather than the raw Docker socket.

---

## 🔒 Network Layer Security (Tailscale)

By using Tailscale, we eliminate the need for traditional VPN servers (like OpenVPN or IPsec) and their associated vulnerabilities.

-   **MagicDNS**: Internal services are accessed via human-readable hostnames that only resolve within the private mesh.
-   **ACL Policies**: Tailscale Access Control Lists (ACLs) can restrict which devices can talk to each other, even within the mesh.
-   **HTTPS inside the Tunnel**: We use a Reverse Proxy to provide valid SSL/TLS certificates for internal hostnames, ensuring traffic is encrypted even *after* it leaves the WireGuard tunnel.

## 🌐 Public Ingress (Cloudflare Tunnel)

A small, deliberately-chosen subset of services is published to the public internet through a [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/) rather than the Tailscale mesh.

-   **Outbound-Initiated**: The `cloudflared` connector dials *out* to Cloudflare, so no inbound ports are opened on the home router — the "no open ports" guarantee still holds.
-   **Identity-Gated**: Every public hostname sits behind a **Cloudflare Access** application requiring **Google OAuth SSO**; unauthenticated requests never reach the origin service.
-   **Hardened Connector**: The container drops all Linux capabilities (`cap_drop: ALL`) and runs with `no-new-privileges`.

## 🔑 Secrets Management

We use a "Clean Repository" strategy to ensure that no sensitive data (API keys, passwords, tokens) is ever committed to version control.

-   **Dynamic Environment Loading**: All sensitive environment variables are loaded dynamically into the shell and injected into the container at runtime.
-   **Consolidated Templates**: Publicly available templates provide the structure of required variables without exposing actual values.
-   **Container Isolation**: Docker Compose files use variable interpolation, ensuring that secrets are only loaded from the host's protected environment.

## 🛡️ Disaster Recovery & Backups

Security isn't just about preventing access—it's also about ensuring data integrity.

-   **Configuration Backups**: Application state and service configurations are backed up with **Restic** (managed via **Backrest**) to **Cloudflare R2** object storage off-site.
-   **Infrastructure as Code**: All service configurations (Prometheus YAML, Grafana Provisioning) are version-controlled, allowing the entire stack to be recreated from scratch in minutes.

---

!!! success "Security Audit Status"
    This infrastructure is under continuous personal review. All dependencies are monitored for vulnerabilities using GitHub's automated security alerts.
