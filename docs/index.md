# Homelab Infrastructure

!!! warning "Public Disclaimer"
    This repository and documentation site represent a **private, non-public-facing homelab infrastructure**. None of the services described here are accessible via the public internet. All connectivity is managed through encrypted, zero-trust mesh networking.

    This documentation is provided as a "Build in Public" initiative for educational purposes, sharing architectural patterns and operational insights.

Welcome to the **Homelab HQ** technical wiki. This project serves as the central documentation hub for a highly automated, containerized homelab environment.

## 🚀 Vision

The goal of this homelab is to provide a robust, enterprise-grade environment for personal services, media, and monitoring, while maintaining a **zero-attack surface** on the public internet.

## 🏗️ Architecture at a Glance

Our infrastructure is built on four core pillars:

1.  **Zero-Trust Networking**: Powered by [Tailscale](https://tailscale.com), providing a private, encrypted mesh network.
2.  **Internal Reverse Proxy**: [Nginx Proxy Manager](https://nginxproxymanager.com) handles SSL/TLS and domain routing *within* the private network.
3.  **Infrastructure as Code**: Everything is containerized and managed via `docker compose` with a unified `Makefile` API.
4.  **Observability**: A full Prometheus/Grafana stack for real-time monitoring and alerting.

## 🛠️ Tech Stack

- **Host OS**: macOS / Linux (Docker Desktop / Engine)
- **Container Orchestration**: Docker Compose
- **VPN / Overlay Network**: Tailscale
- **Monitoring**: Prometheus, Grafana, Loki, Promtail, Beyla
- **Automation**: Apprise, Bridge (custom service)
- **Media**: Jellyfin, Servarr Stack

---

## 🚦 Quick Start for Maintainers

If you are setting up this lab for the first time:

1.  **Initialize Submodules**:
    ```bash
    git submodule update --init --recursive
    ```
2.  **Environment Setup**:
    ```bash
    cp .envrc.example .envrc
    # Edit .envrc with your secrets
    direnv allow
    ```
3.  **Deploy Stack**:
    ```bash
    make up
    ```

[Read the full Security Model :octicons-arrow-right-24:](security.md)
