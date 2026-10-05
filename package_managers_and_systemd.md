Markdown
# Package Managers and systemd Services

## Task
Install Nginx via `apt` or `yum` and manage it with `systemctl` (start, enable, and check status).

---

## 1. Installation via Package Manager

### Ubuntu / Debian (`apt`)
```bash
sudo apt update
sudo apt install -y nginx
RHEL / CentOS (yum / dnf)
Bash
sudo yum install -y epel-release
sudo yum install -y nginx
2. Service Management with systemctl
Start the Service
Bash
sudo systemctl start nginx
Enable Service to Start on Boot
Bash
sudo systemctl enable nginx
Check Service Status
Bash
sudo systemctl status nginx
Additional Service Lifecycle Controls
Bash
# Restart the service
sudo systemctl restart nginx

# Reload configuration without downtime
sudo systemctl reload nginx

# Stop the service
sudo systemctl stop nginx

# Disable auto-start on boot
sudo systemctl disable nginx
3. Verification
Verify the systemd unit state and send a local HTTP request:

Bash
systemctl is-active nginx
systemctl is-enabled nginx
curl -I http://localhost
