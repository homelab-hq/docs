# Infrastructure Overview

Our homelab is designed as a modular, containerized ecosystem where each component is isolated yet interconnected through a secure, private network fabric.

## 🧱 Functional Layers

The infrastructure is organized into distinct logical layers to ensure separation of concerns and high availability.

| Layer | Responsibility | Key Technologies |
| :--- | :--- | :--- |
| **Connectivity** | Secure ingress and mesh networking | Tailscale, WireGuard |
| **Routing** | Service discovery and SSL termination | Nginx Proxy Manager |
| **Observability** | Metrics, logs, and alerting | Prometheus, Grafana, Loki |
| **Automation** | Intelligent event handling | Bridge, Apprise |
| **Application** | User-facing services | Jellyfin, Servarr Stack |

## 🏗️ Conceptual Architecture

The following diagram illustrates how the layers interact to provide a seamless and secure experience.

```mermaid
graph BT
    subgraph AppLayer [Application Layer]
        Media[Media Services]
        Utility[Internal Utilities]
    end

    subgraph LogicLayer [Automation & Logic]
        Bridge[Bridge Service]
        Apprise[Notification Engine]
    end

    subgraph DataLayer [Observability Layer]
        Metrics[Prometheus]
        Logs[Loki]
        Dash[Grafana]
    end

    subgraph GateLayer [Access Layer]
        VPN[Tailscale Gateway]
        Proxy[Reverse Proxy]
    end

    VPN --> Proxy
    Proxy --> AppLayer
    AppLayer --> LogicLayer
    AppLayer -.-> DataLayer
    LogicLayer --> DataLayer
```

---

## 🔄 The Life of a Request

Understanding how a request from a remote device (e.g., your phone on 5G) reaches a private service without any open ports.

```mermaid
sequenceDiagram
    participant U as User Device (Mobile/Laptop)
    participant T1 as Tailscale Node (Client)
    participant T2 as Tailscale Gateway (Homelab)
    participant NPM as Nginx Proxy Manager
    participant S as Private Service (e.g., Grafana)

    U->>T1: Open browser to grafana.ts.debdut.in
    T1->>T1: Resolve hostname via MagicDNS
    T1->>T2: Establish WireGuard tunnel (Encrypted)
    T2->>NPM: Forward request over internal Docker bridge
    NPM->>NPM: Terminate SSL & route by hostname
    NPM->>S: Proxy HTTP request
    S-->>NPM: 200 OK (Response)
    NPM-->>T2: Encrypt response
    T2-->>T1: Secure transmission
    T1-->>U: Render Dashboard
```

## 🛠️ Unified Management

Management of the entire stack is handled through a centralized Command Line Interface (CLI). This interface abstracts the complexity of individual container operations into high-level lifecycle commands:

-   **Deployment**: Orchestrates the startup sequence, ensuring networking is healthy before launching dependent services.
-   **Maintenance**: Handles image updates, configuration reloads, and state persistence.
-   **Diagnostics**: Provides unified log streaming and real-time health telemetry across all layers.
