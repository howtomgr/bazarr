# bazarr Installation Guide

bazarr is a free and open-source subtitle management. Bazarr automates subtitle downloading for Sonarr and Radarr, supporting multiple languages

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
  - RAM: 256MB minimum
  - Storage: 500MB for app
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 6767 (default bazarr port)
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

# Install bazarr
sudo dnf install -y bazarr

# Enable and start service
sudo systemctl enable --now bazarr

# Configure firewall
sudo firewall-cmd --permanent --add-port=6767/tcp
sudo firewall-cmd --reload

# Verify installation
bazarr --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install bazarr
sudo apt install -y bazarr

# Enable and start service
sudo systemctl enable --now bazarr

# Configure firewall
sudo ufw allow 6767

# Verify installation
bazarr --version
```

### Arch Linux

```bash
# Install bazarr
sudo pacman -S bazarr

# Enable and start service
sudo systemctl enable --now bazarr

# Verify installation
bazarr --version
```

### Alpine Linux

```bash
# Install bazarr
apk add --no-cache bazarr

# Enable and start service
rc-update add bazarr default
rc-service bazarr start

# Verify installation
bazarr --version
```

### openSUSE/SLES

```bash
# Install bazarr
sudo zypper install -y bazarr

# Enable and start service
sudo systemctl enable --now bazarr

# Configure firewall
sudo firewall-cmd --permanent --add-port=6767/tcp
sudo firewall-cmd --reload

# Verify installation
bazarr --version
```

### macOS

```bash
# Using Homebrew
brew install bazarr

# Start service
brew services start bazarr

# Verify installation
bazarr --version
```

### FreeBSD

```bash
# Using pkg
pkg install bazarr

# Enable in rc.conf
echo 'bazarr_enable="YES"' >> /etc/rc.conf

# Start service
service bazarr start

# Verify installation
bazarr --version
```

### Windows

```bash
# Using Chocolatey
choco install bazarr

# Or using Scoop
scoop install bazarr

# Verify installation
bazarr --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/bazarr

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
bazarr --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable bazarr

# Start service
sudo systemctl start bazarr

# Stop service
sudo systemctl stop bazarr

# Restart service
sudo systemctl restart bazarr

# Check status
sudo systemctl status bazarr

# View logs
sudo journalctl -u bazarr -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add bazarr default

# Start service
rc-service bazarr start

# Stop service
rc-service bazarr stop

# Restart service
rc-service bazarr restart

# Check status
rc-service bazarr status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'bazarr_enable="YES"' >> /etc/rc.conf

# Start service
service bazarr start

# Stop service
service bazarr stop

# Restart service
service bazarr restart

# Check status
service bazarr status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start bazarr
brew services stop bazarr
brew services restart bazarr

# Check status
brew services list | grep bazarr
```

### Windows Service Manager

```powershell
# Start service
net start bazarr

# Stop service
net stop bazarr

# Using PowerShell
Start-Service bazarr
Stop-Service bazarr
Restart-Service bazarr

# Check status
Get-Service bazarr
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream bazarr_backend {
    server 127.0.0.1:6767;
}

server {
    listen 80;
    server_name bazarr.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name bazarr.example.com;

    ssl_certificate /etc/ssl/certs/bazarr.example.com.crt;
    ssl_certificate_key /etc/ssl/private/bazarr.example.com.key;

    location / {
        proxy_pass http://bazarr_backend;
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
    ServerName bazarr.example.com
    Redirect permanent / https://bazarr.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName bazarr.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/bazarr.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/bazarr.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:6767/
    ProxyPassReverse / http://127.0.0.1:6767/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend bazarr_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/bazarr.pem
    redirect scheme https if !{ ssl_fc }
    default_backend bazarr_backend

backend bazarr_backend
    balance roundrobin
    server bazarr1 127.0.0.1:6767 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R bazarr:bazarr /etc/bazarr
sudo chmod 750 /etc/bazarr

# Configure firewall
sudo firewall-cmd --permanent --add-port=6767/tcp
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
sudo systemctl status bazarr

# View logs
sudo journalctl -u bazarr -f

# Monitor resource usage
top -p $(pgrep bazarr)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/bazarr"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/bazarr-backup-$DATE.tar.gz" /etc/bazarr /var/lib/bazarr

echo "Backup completed: $BACKUP_DIR/bazarr-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop bazarr

# Restore from backup
tar -xzf /backup/bazarr/bazarr-backup-*.tar.gz -C /

# Start service
sudo systemctl start bazarr
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u bazarr -n 100
sudo tail -f /var/log/bazarr/bazarr.log

# Check configuration
bazarr --version

# Check permissions
ls -la /etc/bazarr
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 6767

# Test connectivity
telnet localhost 6767

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep bazarr)

# Check disk I/O
iotop -p $(pgrep bazarr)

# Check connections
ss -an | grep 6767
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  bazarr:
    image: bazarr:latest
    ports:
      - "6767:6767"
    volumes:
      - ./config:/etc/bazarr
      - ./data:/var/lib/bazarr
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update bazarr

# Debian/Ubuntu
sudo apt update && sudo apt upgrade bazarr

# Arch Linux
sudo pacman -Syu bazarr

# Alpine Linux
apk update && apk upgrade bazarr

# openSUSE
sudo zypper update bazarr

# FreeBSD
pkg update && pkg upgrade bazarr

# Always backup before updates
tar -czf /backup/bazarr-pre-update-$(date +%Y%m%d).tar.gz /etc/bazarr

# Restart after updates
sudo systemctl restart bazarr
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/bazarr

# Clean old logs
find /var/log/bazarr -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/bazarr
```

## Additional Resources

- Official Documentation: https://docs.bazarr.org/
- GitHub Repository: https://github.com/bazarr/bazarr
- Community Forum: https://forum.bazarr.org/
- Best Practices Guide: https://docs.bazarr.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
