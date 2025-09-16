# wiki-js Installation Guide

wiki-js is a free and open-source modern wiki platform. Wiki.js provides modern, lightweight wiki with git synchronization

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 512MB minimum
  - Storage: 1GB for data
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 3000 (default wiki-js port)
  - None
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install wiki-js
sudo dnf install -y wiki-js

# Enable and start service
sudo systemctl enable --now wiki-js

# Configure firewall
sudo firewall-cmd --permanent --add-port=3000/tcp
sudo firewall-cmd --reload

# Verify installation
wiki-js --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install wiki-js
sudo apt install -y wiki-js

# Enable and start service
sudo systemctl enable --now wiki-js

# Configure firewall
sudo ufw allow 3000

# Verify installation
wiki-js --version
```

### Arch Linux

```bash
# Install wiki-js
sudo pacman -S wiki-js

# Enable and start service
sudo systemctl enable --now wiki-js

# Verify installation
wiki-js --version
```

### Alpine Linux

```bash
# Install wiki-js
apk add --no-cache wiki-js

# Enable and start service
rc-update add wiki-js default
rc-service wiki-js start

# Verify installation
wiki-js --version
```

### openSUSE/SLES

```bash
# Install wiki-js
sudo zypper install -y wiki-js

# Enable and start service
sudo systemctl enable --now wiki-js

# Configure firewall
sudo firewall-cmd --permanent --add-port=3000/tcp
sudo firewall-cmd --reload

# Verify installation
wiki-js --version
```

### macOS

```bash
# Using Homebrew
brew install wiki-js

# Start service
brew services start wiki-js

# Verify installation
wiki-js --version
```

### FreeBSD

```bash
# Using pkg
pkg install wiki-js

# Enable in rc.conf
echo 'wiki-js_enable="YES"' >> /etc/rc.conf

# Start service
service wiki-js start

# Verify installation
wiki-js --version
```

### Windows

```bash
# Using Chocolatey
choco install wiki-js

# Or using Scoop
scoop install wiki-js

# Verify installation
wiki-js --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/wiki-js

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
wiki-js --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable wiki-js

# Start service
sudo systemctl start wiki-js

# Stop service
sudo systemctl stop wiki-js

# Restart service
sudo systemctl restart wiki-js

# Check status
sudo systemctl status wiki-js

# View logs
sudo journalctl -u wiki-js -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add wiki-js default

# Start service
rc-service wiki-js start

# Stop service
rc-service wiki-js stop

# Restart service
rc-service wiki-js restart

# Check status
rc-service wiki-js status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'wiki-js_enable="YES"' >> /etc/rc.conf

# Start service
service wiki-js start

# Stop service
service wiki-js stop

# Restart service
service wiki-js restart

# Check status
service wiki-js status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start wiki-js
brew services stop wiki-js
brew services restart wiki-js

# Check status
brew services list | grep wiki-js
```

### Windows Service Manager

```powershell
# Start service
net start wiki-js

# Stop service
net stop wiki-js

# Using PowerShell
Start-Service wiki-js
Stop-Service wiki-js
Restart-Service wiki-js

# Check status
Get-Service wiki-js
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream wiki-js_backend {
    server 127.0.0.1:3000;
}

server {
    listen 80;
    server_name wiki-js.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name wiki-js.example.com;

    ssl_certificate /etc/ssl/certs/wiki-js.example.com.crt;
    ssl_certificate_key /etc/ssl/private/wiki-js.example.com.key;

    location / {
        proxy_pass http://wiki-js_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName wiki-js.example.com
    Redirect permanent / https://wiki-js.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName wiki-js.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/wiki-js.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/wiki-js.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:3000/
    ProxyPassReverse / http://127.0.0.1:3000/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend wiki-js_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/wiki-js.pem
    redirect scheme https if !{ ssl_fc }
    default_backend wiki-js_backend

backend wiki-js_backend
    balance roundrobin
    server wiki-js1 127.0.0.1:3000 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R wiki-js:wiki-js /etc/wiki-js
sudo chmod 750 /etc/wiki-js

# Configure firewall
sudo firewall-cmd --permanent --add-port=3000/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status wiki-js

# View logs
sudo journalctl -u wiki-js -f

# Monitor resource usage
top -p $(pgrep wiki-js)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/wiki-js"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/wiki-js-backup-$DATE.tar.gz" /etc/wiki-js /var/lib/wiki-js

echo "Backup completed: $BACKUP_DIR/wiki-js-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop wiki-js

# Restore from backup
tar -xzf /backup/wiki-js/wiki-js-backup-*.tar.gz -C /

# Start service
sudo systemctl start wiki-js
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u wiki-js -n 100
sudo tail -f /var/log/wiki-js/wiki-js.log

# Check configuration
wiki-js --version

# Check permissions
ls -la /etc/wiki-js
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 3000

# Test connectivity
telnet localhost 3000

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep wiki-js)

# Check disk I/O
iotop -p $(pgrep wiki-js)

# Check connections
ss -an | grep 3000
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  wiki-js:
    image: wiki-js:latest
    ports:
      - "3000:3000"
    volumes:
      - ./config:/etc/wiki-js
      - ./data:/var/lib/wiki-js
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update wiki-js

# Debian/Ubuntu
sudo apt update && sudo apt upgrade wiki-js

# Arch Linux
sudo pacman -Syu wiki-js

# Alpine Linux
apk update && apk upgrade wiki-js

# openSUSE
sudo zypper update wiki-js

# FreeBSD
pkg update && pkg upgrade wiki-js

# Always backup before updates
tar -czf /backup/wiki-js-pre-update-$(date +%Y%m%d).tar.gz /etc/wiki-js

# Restart after updates
sudo systemctl restart wiki-js
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/wiki-js

# Clean old logs
find /var/log/wiki-js -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/wiki-js
```

## Additional Resources

- Official Documentation: https://docs.wiki-js.org/
- GitHub Repository: https://github.com/wiki-js/wiki-js
- Community Forum: https://forum.wiki-js.org/
- Best Practices Guide: https://docs.wiki-js.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
