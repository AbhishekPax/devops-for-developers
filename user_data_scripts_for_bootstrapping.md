# Task: User-Data Scripts for Bootstrapping

## Objective
Launch an Amazon EC2 Linux instance configured with an EC2 User-Data shell script that automatically installs, configures, and starts the Nginx web server on initial boot.

---

## 1. What is EC2 User-Data?
EC2 user-data allows you to pass shell scripts or cloud-init directives to an instance at launch time.
- **Execution:** Runs as the `root` user during the final phase of the first instance boot.
- **Logs & Troubleshooting:** Script execution output is logged to `/var/log/cloud-init-output.log`.

---

## 2. Bootstrapping User-Data Script

### For Amazon Linux 2023:
```bash
#!/bin/bash
# Update installed packages
dnf update -y

# Install Nginx
dnf install -y nginx

# Start and enable Nginx service to persist across reboots
systemctl start nginx
systemctl enable nginx

# Create a custom landing page
cat <<EOF> /usr/share/nginx/html/index.html
<!DOCTYPE html>
<html>
<head>
    <title>Bootstrapped EC2 Instance</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; margin-top: 100px; }
        h1 { color: #232f3e; }
        p { color: #555; }
    </style>
</head>
<body>
    <h1>Automated Deployment via EC2 User-Data</h1>
    <p>Nginx was installed and started automatically on first boot.</p>
</body>
</html>
EOF
