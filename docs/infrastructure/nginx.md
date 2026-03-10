# Internal Reverse Proxy

The **Nginx Proxy Manager** is our centralized gateway for all internal web services, providing domain routing and SSL/TLS termination.

## 🌉 Role

While Tailscale provides the secure tunnel into the network, Nginx Proxy Manager (NPM) provides the application-level routing.

- **SSL/TLS Termination**: Provides HTTPS certificates for internal services using Let's Encrypt and Acme.
- **Internal Domain Routing**: Maps internal hostnames (e.g., `grafana.ts.debdut.in`) to their respective Docker containers and ports.
- **Load Balancing**: Can distribute traffic between multiple service instances where needed.

## 🛠️ Docker Configuration

NPM runs as a container within the homelab stack, listening on ports 80 and 443 of the internal Docker bridge network.

| Component | Port | Description |
| :--- | :--- | :--- |
| **HTTP** | 80 | Redirects to HTTPS. |
| **HTTPS** | 443 | Terminates SSL/TLS. |
| **Admin Web UI** | 81 | Centralized management dashboard. |

## 🔒 Security Configuration

- **Restricted Access**: The Admin Web UI is only accessible via Tailscale and is protected by MFA.
- **HTTP/2 Support**: Enabled for all internal services to provide high-performance, multiplexed connections.
- **HSTS Policy**: Enforced to ensure browsers always use a secure connection for all internal domains.
