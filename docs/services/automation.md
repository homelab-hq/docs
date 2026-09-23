# Automation & Notifications

Automation is the glue that connects our infrastructure, media, and monitoring stacks.

## 🔔 Apprise

**Apprise** is our central notification server, providing a unified API for sending alerts to over 100+ services.

- **Unified Format**: All internal services send notifications to `http://apprise:8000/notify/`.
- **Telegram Integration**: Alerts are routed to a private Telegram bot via the `TELEGRAM_URL` environment variable.

## 🛠️ Workflows

### **Monitoring Workflow**
1.  **Grafana** detects a metric anomaly.
2.  An alert is sent to **Apprise**.
3.  **Apprise** delivers the alert to a private Telegram channel.

### **Media Workflow**
1.  **Sonarr / Radarr** completes a download.
2.  The `post_import.py` post-import script runs (remux + codec validation via `mkv2mp4.py`).
3.  A "Media Ready" notification is sent directly to **Apprise**, which delivers it to Telegram.
