# Security Model

The security of this homelab is built on a **Zero-Trust Networking Architecture**. This means we do not trust any network by default—even our local home network—and we rely on verified identity and encryption for all communications.

## 🛡️ Core Security Principles

1.  **Zero Attack Surface**: No ports are forwarded on the internet-facing router. All inbound traffic must pass through an encrypted VPN tunnel.
2.  **Encrypted Mesh Networking**: Every node is part of a [Tailscale](https://tailscale.com) mesh network, providing end-to-end WireGuard® encryption.
3.  **Authenticated Access**: Identity-based authentication (via OIDC/SSO in Tailscale) is required for all access.
4.  **Least Privilege**: Each Docker container runs with limited CPU/Memory resources and restricted network access where possible.

---

## 🔒 Network Layer Security (Tailscale)

By using Tailscale, we eliminate the need for traditional VPN servers (like OpenVPN or IPsec) and their associated vulnerabilities.

-   **MagicDNS**: Internal services are accessed via human-readable hostnames (e.g., `grafana.ts.debdut.in`) that only resolve within the private mesh.
-   **ACL Policies**: Tailscale Access Control Lists (ACLs) can restrict which devices can talk to each other, even within the mesh.
-   **HTTPS inside the Tunnel**: We use Nginx Proxy Manager to provide valid SSL/TLS certificates for internal hostnames, ensuring traffic is encrypted even *after* it leaves the WireGuard tunnel.

## 🔑 Secrets Management

We use a "Clean Repository" strategy to ensure that no sensitive data (API keys, passwords, tokens) is ever committed to version control.

-   **`.envrc` (direnv)**: All environment variables are stored in a local `.envrc` file, which is ignored by Git.
-   **Consolidated Templates**: A public `.envrc.example` provides the structure of required variables without exposing actual values.
-   **Docker Secrets (Interpolation)**: Docker Compose files use `${VARIABLE_NAME}` interpolation, ensuring that secrets are only loaded at runtime from the host's environment.

## 🛡️ Disaster Recovery & Backups

Security isn't just about preventing access—it's also about ensuring data integrity.

-   **Nginx Proxy Manager Backups**: A `hosts_backup.json` is maintained to quickly recreate the reverse proxy mapping.
-   **Configuration as Code**: All service configurations (Prometheus YAML, Grafana Provisioning) are version-controlled, allowing the entire stack to be recreated from scratch in minutes.

---

!!! success "Security Audit Status"
    This infrastructure is under continuous personal review. All dependencies are monitored for vulnerabilities using GitHub's automated security alerts.
