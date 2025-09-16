# freshrss Installation Guide

freshrss is a free and open-source self-hosted RSS feed aggregator. FreshRSS is a free, self-hostable feeds aggregator with a responsive design, serving as an alternative to Feedly or Google Reader

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
  - Storage: 100MB + feed data
  - Network: HTTP/HTTPS for feeds
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80/443 (default freshrss port)
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

# Install freshrss
sudo dnf install -y freshrss

# Enable and start service
sudo systemctl enable --now freshrss

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/443/tcp
sudo firewall-cmd --reload

# Verify installation
freshrss --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install freshrss
sudo apt install -y freshrss

# Enable and start service
sudo systemctl enable --now freshrss

# Configure firewall
sudo ufw allow 80/443

# Verify installation
freshrss --version
```

### Arch Linux

```bash
# Install freshrss
sudo pacman -S freshrss

# Enable and start service
sudo systemctl enable --now freshrss

# Verify installation
freshrss --version
```

### Alpine Linux

```bash
# Install freshrss
apk add --no-cache freshrss

# Enable and start service
rc-update add freshrss default
rc-service freshrss start

# Verify installation
freshrss --version
```

### openSUSE/SLES

```bash
# Install freshrss
sudo zypper install -y freshrss

# Enable and start service
sudo systemctl enable --now freshrss

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/443/tcp
sudo firewall-cmd --reload

# Verify installation
freshrss --version
```

### macOS

```bash
# Using Homebrew
brew install freshrss

# Start service
brew services start freshrss

# Verify installation
freshrss --version
```

### FreeBSD

```bash
# Using pkg
pkg install freshrss

# Enable in rc.conf
echo 'freshrss_enable="YES"' >> /etc/rc.conf

# Start service
service freshrss start

# Verify installation
freshrss --version
```

### Windows

```bash
# Using Chocolatey
choco install freshrss

# Or using Scoop
scoop install freshrss

# Verify installation
freshrss --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/freshrss

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
freshrss --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable freshrss

# Start service
sudo systemctl start freshrss

# Stop service
sudo systemctl stop freshrss

# Restart service
sudo systemctl restart freshrss

# Check status
sudo systemctl status freshrss

# View logs
sudo journalctl -u freshrss -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add freshrss default

# Start service
rc-service freshrss start

# Stop service
rc-service freshrss stop

# Restart service
rc-service freshrss restart

# Check status
rc-service freshrss status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'freshrss_enable="YES"' >> /etc/rc.conf

# Start service
service freshrss start

# Stop service
service freshrss stop

# Restart service
service freshrss restart

# Check status
service freshrss status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start freshrss
brew services stop freshrss
brew services restart freshrss

# Check status
brew services list | grep freshrss
```

### Windows Service Manager

```powershell
# Start service
net start freshrss

# Stop service
net stop freshrss

# Using PowerShell
Start-Service freshrss
Stop-Service freshrss
Restart-Service freshrss

# Check status
Get-Service freshrss
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream freshrss_backend {
    server 127.0.0.1:80/443;
}

server {
    listen 80;
    server_name freshrss.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name freshrss.example.com;

    ssl_certificate /etc/ssl/certs/freshrss.example.com.crt;
    ssl_certificate_key /etc/ssl/private/freshrss.example.com.key;

    location / {
        proxy_pass http://freshrss_backend;
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
    ServerName freshrss.example.com
    Redirect permanent / https://freshrss.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName freshrss.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/freshrss.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/freshrss.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/443/
    ProxyPassReverse / http://127.0.0.1:80/443/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend freshrss_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/freshrss.pem
    redirect scheme https if !{ ssl_fc }
    default_backend freshrss_backend

backend freshrss_backend
    balance roundrobin
    server freshrss1 127.0.0.1:80/443 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R freshrss:freshrss /etc/freshrss
sudo chmod 750 /etc/freshrss

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/443/tcp
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
sudo systemctl status freshrss

# View logs
sudo journalctl -u freshrss -f

# Monitor resource usage
top -p $(pgrep freshrss)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/freshrss"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/freshrss-backup-$DATE.tar.gz" /etc/freshrss /var/lib/freshrss

echo "Backup completed: $BACKUP_DIR/freshrss-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop freshrss

# Restore from backup
tar -xzf /backup/freshrss/freshrss-backup-*.tar.gz -C /

# Start service
sudo systemctl start freshrss
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u freshrss -n 100
sudo tail -f /var/log/freshrss/freshrss.log

# Check configuration
freshrss --version

# Check permissions
ls -la /etc/freshrss
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80/443

# Test connectivity
telnet localhost 80/443

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep freshrss)

# Check disk I/O
iotop -p $(pgrep freshrss)

# Check connections
ss -an | grep 80/443
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  freshrss:
    image: freshrss:latest
    ports:
      - "80/443:80/443"
    volumes:
      - ./config:/etc/freshrss
      - ./data:/var/lib/freshrss
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update freshrss

# Debian/Ubuntu
sudo apt update && sudo apt upgrade freshrss

# Arch Linux
sudo pacman -Syu freshrss

# Alpine Linux
apk update && apk upgrade freshrss

# openSUSE
sudo zypper update freshrss

# FreeBSD
pkg update && pkg upgrade freshrss

# Always backup before updates
tar -czf /backup/freshrss-pre-update-$(date +%Y%m%d).tar.gz /etc/freshrss

# Restart after updates
sudo systemctl restart freshrss
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/freshrss

# Clean old logs
find /var/log/freshrss -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/freshrss
```

## Additional Resources

- Official Documentation: https://docs.freshrss.org/
- GitHub Repository: https://github.com/freshrss/freshrss
- Community Forum: https://forum.freshrss.org/
- Best Practices Guide: https://docs.freshrss.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
