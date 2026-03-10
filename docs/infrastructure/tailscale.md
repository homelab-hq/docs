# Networking & VPN

Our networking strategy is built on **Zero-Trust Principles**, where all inbound traffic is encrypted and authenticated through a private mesh network.

## 🕸️ Tailscale

**Tailscale** is the backbone of our secure networking. It creates an overlay network (Tailnet) that connects all devices—from cloud servers to local Docker containers—without requiring port forwarding.

- **WireGuard® Powered**: Every connection is encrypted end-to-end.
- **Identity-based Access**: Users authenticate with their OIDC/SSO provider.
- **MagicDNS**: Automatically assigns human-readable hostnames to devices (e.g., `homelab.ts.debdut.in`).

## 🛠️ Docker Integration

Tailscale runs as a container within the homelab stack, providing a gateway into the internal Docker network.

``` mermaid
graph LR
    subgraph Public Internet
        U[User on Mobile/Laptop]
    end

    subgraph Tailnet
        U -- WireGuard Tunnel --> T[Tailscale Container]
    end

    subgraph Internal Network
        T -- Route --> NPM[Nginx Proxy Manager]
        NPM -- HTTP --> S[Service: Grafana]
    end
```

## 🔒 Security Advantages

1.  **No Port Forwarding**: The internet-facing router remains completely closed.
2.  **Private Hostnames**: Services like `prometheus.ts.debdut.in` are only resolvable and accessible when connected to the Tailnet.
3.  **Encrypted Internode Traffic**: Communication between the Tailscale gateway and other services is isolated within the internal Docker network (`homelab` bridge).
