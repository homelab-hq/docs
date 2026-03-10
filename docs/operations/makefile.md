# Infrastructure Management (CLI API)

The infrastructure is managed through a unified CLI, providing an industry-standard interface for all lifecycle operations.

## 🚀 Key Commands

| Command | Role | Description |
| :--- | :--- | :--- |
| **make up** | Start | Initiates the infrastructure in dependency order. |
| **make down** | Stop | Gracefully stops all services and infrastructure. |
| **make restart** | Reset | Performs a full cycle: down followed by up. |
| **make status** | Health | Shows combined health telemetry of all containers. |
| **make update** | Full Update | Pulls latest images and performs a rolling restart. |
| **make logs** | Observability | Aggregates logs from across all service layers. |

## 🛠️ Usage Examples

### **Start All Services**
```bash
make up
```

### **Restart a Specific Service**
```bash
make restart <service_name>
```

### **Follow Logs for All Monitoring Components**
```bash
make logs monitoring
```

## ⚙️ Advanced Features

- **Dependency Order**: Critical infrastructure (Tailscale, Nginx Proxy Manager) is prioritized before application layers.
- **Service-Specific Control**: Most commands support an optional service argument for surgical operations.
- **Visual Feedback**: Provides color-coded, real-time output for all management steps.
