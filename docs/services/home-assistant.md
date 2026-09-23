# Home Automation (Home Assistant)

**Home Assistant** is the smart-home hub, unifying devices and automations behind a single private dashboard.

## 🏠 Role

- **Device Hub**: Central control for smart-home devices and integrations.
- **Automation**: Event- and schedule-driven automations across connected devices.
- **Dashboards**: Custom Lovelace UIs for at-a-glance status and control.
- **Access**: [homeassistant.ts.debdut.in](http://homeassistant.ts.debdut.in)

## 🛠️ Configuration

| Setting | Value |
| :--- | :--- |
| **Internal Port** | 8123 |
| **Config** | `./config` |
| **Resources** | 512 MB / 0.5 CPU |

The container image is `home-assistant:stable`. Configuration lives in `./config` and is version-controlled (secrets excluded).

## 🔒 Security

- Reached only through Nginx Proxy Manager over Tailscale — no host ports exposed.
- Runs with `cap_drop: ALL`, a minimal capability add set, and `no-new-privileges`.

!!! note "Shared Google OAuth"
    The Google OAuth client used for [Cloudflare Access](../infrastructure/cloudflared.md) is shared with Home Assistant's Google integration.
