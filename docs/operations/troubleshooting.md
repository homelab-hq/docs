# Troubleshooting Homelab Operations

Common issues and their resolutions for the homelab infrastructure.

## 🛠️ Infrastructure Issues

### **Tailscale Connection Failures**
-   **Symptom**: Internal hostnames don't resolve.
-   **Resolution**: Ensure your device is logged into the Tailnet and MagicDNS is enabled.
-   **Check**: Run `tailscale status` in the terminal to verify your connection.

### **Docker Network Conflicts**
-   **Symptom**: Containers fail to start with a "network already exists" error.
-   **Resolution**: Perform a full cleanup to reset the bridge network.
-   **Check**: Use Docker's networking commands to identify orphan homelab networks.

### **SSL Certificate Failures**
-   **Symptom**: Reverse Proxy fails to renew certificates.
-   **Resolution**: Ensure the reverse proxy container can access external renewal services.
-   **Check**: Verify logs for renewal errors.

## ⚙️ Service-Specific Issues

### **Monitoring Stack Resource Pressure**
-   **Symptom**: Monitoring containers are frequently restarted.
-   **Resolution**: Adjust resource limits in the orchestration configuration.
-   **Check**: Use container statistics to monitor real-time memory and CPU usage.

### **Media Automation Failures**
-   **Symptom**: Post-processing fails to run after a download completes.
-   **Resolution**: Ensure the post-import environment and dependencies are correctly initialized.
-   **Check**: Verify service-specific logs for post-import execution errors.
