# prestashop Installation Guide

prestashop is a free and open-source e-commerce solution. PrestaShop provides free e-commerce solution

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
  - CPU: 2+ cores
  - RAM: 2GB minimum
  - Storage: 10GB for data
  - Network: HTTP/HTTPS
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default prestashop port)
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

# Install prestashop
sudo dnf install -y prestashop

# Enable and start service
sudo systemctl enable --now prestashop

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
prestashop --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install prestashop
sudo apt install -y prestashop

# Enable and start service
sudo systemctl enable --now prestashop

# Configure firewall
sudo ufw allow 80

# Verify installation
prestashop --version
```

### Arch Linux

```bash
# Install prestashop
sudo pacman -S prestashop

# Enable and start service
sudo systemctl enable --now prestashop

# Verify installation
prestashop --version
```

### Alpine Linux

```bash
# Install prestashop
apk add --no-cache prestashop

# Enable and start service
rc-update add prestashop default
rc-service prestashop start

# Verify installation
prestashop --version
```

### openSUSE/SLES

```bash
# Install prestashop
sudo zypper install -y prestashop

# Enable and start service
sudo systemctl enable --now prestashop

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
prestashop --version
```

### macOS

```bash
# Using Homebrew
brew install prestashop

# Start service
brew services start prestashop

# Verify installation
prestashop --version
```

### FreeBSD

```bash
# Using pkg
pkg install prestashop

# Enable in rc.conf
echo 'prestashop_enable="YES"' >> /etc/rc.conf

# Start service
service prestashop start

# Verify installation
prestashop --version
```

### Windows

```bash
# Using Chocolatey
choco install prestashop

# Or using Scoop
scoop install prestashop

# Verify installation
prestashop --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/prestashop

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
prestashop --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable prestashop

# Start service
sudo systemctl start prestashop

# Stop service
sudo systemctl stop prestashop

# Restart service
sudo systemctl restart prestashop

# Check status
sudo systemctl status prestashop

# View logs
sudo journalctl -u prestashop -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add prestashop default

# Start service
rc-service prestashop start

# Stop service
rc-service prestashop stop

# Restart service
rc-service prestashop restart

# Check status
rc-service prestashop status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'prestashop_enable="YES"' >> /etc/rc.conf

# Start service
service prestashop start

# Stop service
service prestashop stop

# Restart service
service prestashop restart

# Check status
service prestashop status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start prestashop
brew services stop prestashop
brew services restart prestashop

# Check status
brew services list | grep prestashop
```

### Windows Service Manager

```powershell
# Start service
net start prestashop

# Stop service
net stop prestashop

# Using PowerShell
Start-Service prestashop
Stop-Service prestashop
Restart-Service prestashop

# Check status
Get-Service prestashop
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream prestashop_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name prestashop.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name prestashop.example.com;

    ssl_certificate /etc/ssl/certs/prestashop.example.com.crt;
    ssl_certificate_key /etc/ssl/private/prestashop.example.com.key;

    location / {
        proxy_pass http://prestashop_backend;
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
    ServerName prestashop.example.com
    Redirect permanent / https://prestashop.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName prestashop.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/prestashop.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/prestashop.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend prestashop_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/prestashop.pem
    redirect scheme https if !{ ssl_fc }
    default_backend prestashop_backend

backend prestashop_backend
    balance roundrobin
    server prestashop1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R prestashop:prestashop /etc/prestashop
sudo chmod 750 /etc/prestashop

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
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
sudo systemctl status prestashop

# View logs
sudo journalctl -u prestashop -f

# Monitor resource usage
top -p $(pgrep prestashop)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/prestashop"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/prestashop-backup-$DATE.tar.gz" /etc/prestashop /var/lib/prestashop

echo "Backup completed: $BACKUP_DIR/prestashop-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop prestashop

# Restore from backup
tar -xzf /backup/prestashop/prestashop-backup-*.tar.gz -C /

# Start service
sudo systemctl start prestashop
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u prestashop -n 100
sudo tail -f /var/log/prestashop/prestashop.log

# Check configuration
prestashop --version

# Check permissions
ls -la /etc/prestashop
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80

# Test connectivity
telnet localhost 80

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep prestashop)

# Check disk I/O
iotop -p $(pgrep prestashop)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  prestashop:
    image: prestashop:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/prestashop
      - ./data:/var/lib/prestashop
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update prestashop

# Debian/Ubuntu
sudo apt update && sudo apt upgrade prestashop

# Arch Linux
sudo pacman -Syu prestashop

# Alpine Linux
apk update && apk upgrade prestashop

# openSUSE
sudo zypper update prestashop

# FreeBSD
pkg update && pkg upgrade prestashop

# Always backup before updates
tar -czf /backup/prestashop-pre-update-$(date +%Y%m%d).tar.gz /etc/prestashop

# Restart after updates
sudo systemctl restart prestashop
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/prestashop

# Clean old logs
find /var/log/prestashop -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/prestashop
```

## Additional Resources

- Official Documentation: https://docs.prestashop.org/
- GitHub Repository: https://github.com/prestashop/prestashop
- Community Forum: https://forum.prestashop.org/
- Best Practices Guide: https://docs.prestashop.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
