# Homelab Documentation Wiki

[![Build Status](https://github.com/homelab-hq/docs/actions/workflows/deploy-docs.yml/badge.svg)](https://github.com/homelab-hq/docs/actions/workflows/deploy-docs.yml)
[![Documentation](https://img.shields.io/badge/docs-homelab.debdut.in-teal?style=flat-square)](https://homelab.debdut.in/)
[![Built with MkDocs](https://img.shields.io/badge/built%20with-MkDocs-blue?style=flat-square)](https://www.mkdocs.org/)

This repository contains the source files for the **Homelab HQ** technical wiki, accessible at:
👉 **[https://homelab.debdut.in/](https://homelab.debdut.in/)**

## 📖 Content

This wiki provides a comprehensive overview of a private, zero-trust homelab infrastructure, including:

-   **Architecture**: High-level and detailed component diagrams.
-   **Security Model**: Deep-dive into Tailscale, zero-trust principles, and secrets management.
-   **Service Catalog**: Documentation for monitoring, media automation, and custom bridging services.
-   **Operations**: Guides for stack management using a unified CLI (Makefile).

## 🚀 Vision

The goal of this wiki is to "Build in Public" by sharing architectural patterns and operational insights without exposing private implementation details or sensitive credentials.

## 🛠️ Technology Stack

-   **Generator**: [MkDocs](https://www.mkdocs.org/)
-   **Theme**: [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)
-   **Diagrams**: [Mermaid.js](https://mermaid.js.org/)
-   **Deployment**: GitHub Actions + GitHub Pages

## 🛡️ Privacy & Security

This is a public documentation repository for a **private** infrastructure. None of the services documented here are accessible from the public internet. All connectivity is managed through encrypted tunnels.

---
© 2026 Debdut Goswami
