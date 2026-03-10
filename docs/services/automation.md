# Automation & Notifications

Automation is the glue that connects our infrastructure, media, and monitoring stacks.

## 🔔 Apprise

**Apprise** is our central notification server, providing a unified API for sending alerts to over 100+ services.

- **Unified Format**: All internal services send notifications to `http://apprise:8000/notify/`.
- **Telegram Integration**: Alerts are routed to a private Telegram bot via the `TELEGRAM_URL` environment variable.

## 🌉 Bridge

**Bridge** is a custom Python service that acts as an intelligent intermediary between our stacks.

| Role | Description |
| :--- | :--- |
| **Health Proxy** | Buffers and debounces healthcheck alerts. |
| **Webhook Normalization** | Converts disparate webhook formats into standard Apprise notifications. |
| **Alert Enrichment** | Adds context (e.g., container uptime) to monitoring alerts. |

## 🛠️ Workflows

### **Monitoring Workflow**
1.  **Grafana** detects a metric anomaly.
2.  An alert is sent to **Apprise**.
3.  **Apprise** delivers the alert to a private Telegram channel.

### **Media Workflow**
1.  **Sonarr** completes a download.
2.  A post-import script triggers `mkv2mp4.py`.
3.  **Bridge** sends a "Media Ready" notification to Telegram via **Apprise**.
