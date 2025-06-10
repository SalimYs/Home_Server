# 🏠 Home Server Management Guide

![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![SSH](https://img.shields.io/badge/SSH-4D4D4D?style=for-the-badge&logo=gnubash&logoColor=white)
![Samba](https://img.shields.io/badge/Samba-AA1E1E?style=for-the-badge&logo=samba&logoColor=white)
![Jellyfin](https://img.shields.io/badge/Jellyfin-00A4DC?style=for-the-badge&logo=jellyfin&logoColor=white)
![OpenVPN](https://img.shields.io/badge/OpenVPN-EA7E20?style=for-the-badge&logo=openvpn&logoColor=white)
![Bash](https://img.shields.io/badge/Shell_Script-121011?style=for-the-badge&logo=gnu-bash&logoColor=white)
![Networking](https://img.shields.io/badge/Networking-0078D4?style=for-the-badge&logo=cisco&logoColor=white)

A comprehensive guide for setting up and managing a home server with Ubuntu Server, including remote access, media streaming, file sharing, and network services.

## 🎯 Project Goals & Learning Objectives

This project serves as a hands-on learning experience covering multiple domains of IT and system administration:

### 🌐 **Networking**
- Understanding TCP/IP fundamentals and network configuration
- Static IP assignment and DHCP reservation concepts
- Port forwarding and firewall management
- Wake-on-LAN implementation and network packet magic
- VPN tunneling and secure remote access

### 🔒 **Security**
- SSH key-based authentication and hardening
- Firewall configuration with UFW
- User permission management and access control
- VPN server setup for secure remote connections
- Security best practices for home servers

### 🔧 **DevOps & System Administration**
- Linux server installation and configuration
- Service management with systemd
- Automated deployments using shell scripts
- System monitoring and log analysis
- Backup strategies and disaster recovery

### 💾 **Data Management**
- Network file sharing with Samba/CIFS
- Media server setup and content organization
- Storage management and disk partitioning
- Data backup and synchronization strategies

### 🛠️ **Infrastructure as Code**
- Configuration management through scripts
- Reproducible server deployments
- Documentation-driven development
- Version control for infrastructure configurations

## 📋 Table of Contents

- [Overview](#overview)
- [Hardware Requirements](#hardware-requirements)
- [Initial Setup](#initial-setup)
- [SSH Configuration](#ssh-configuration)
- [Static IP Configuration](#static-ip-configuration)
- [Wake-on-LAN Setup](#wake-on-lan-setup)
- [Services Installation](#services-installation)
  - [Samba File Sharing](#samba-file-sharing)
  - [Jellyfin Media Server](#jellyfin-media-server)
  - [VPN Server](#vpn-server)
- [Security Considerations](#security-considerations)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)

## 🔍 Overview

This repository contains scripts and documentation for setting up a home server using Ubuntu Server Minimized. The setup includes:

- **Remote SSH access** from main machine to server
- **Static IP configuration** for reliable network access
- **Samba file sharing** for network storage
- **Jellyfin media server** for streaming content
- **VPN server** for secure remote access
- **Wake-on-LAN** for remote server power management

## 💻 Hardware Requirements

### 🖥️ **Server Machine (Spare Laptop/Desktop)**
- **CPU**: Minimum dual-core processor (Intel i3/AMD equivalent or better)
  - Recommended: Quad-core for better Jellyfin transcoding performance
- **RAM**: Minimum 4GB DDR3/DDR4
  - Recommended: 8GB+ for multiple services and media transcoding
- **Storage**: 
  - System: 64GB+ SSD/HDD for OS and applications
  - Media: Additional drives for content storage (optional)
  - Recommended: 256GB+ SSD for system, separate drives for media
- **Network**: Gigabit Ethernet port (preferred) or Wi-Fi capability
- **Features**: 
  - Wake-on-LAN support (most modern hardware)
  - Multiple USB ports for external storage
  - HDMI/Display output for local troubleshooting

### 💻 **Main Administration Machine**
- **OS**: Windows 10/11, macOS, or Linux distribution
- **Network**: Ethernet or Wi-Fi connection to same network
- **Software**: SSH client, web browser, text editor
- **Optional**: Wake-on-LAN utility, VPN client

### 🌐 **Network Infrastructure**
- **Router**: Consumer router with DHCP server capability
- **Internet**: Broadband connection (for remote VPN access)
- **Optional**: Managed switch for additional ports
- **Cables**: Ethernet cables for wired connections

### 🔌 **Power & Environment**
- **UPS**: Uninterruptible Power Supply (recommended for data protection)
- **Ventilation**: Adequate airflow for 24/7 operation
- **Physical Security**: Secure location for server hardware

## 🚀 Quick Start Guide

### Option 1: Automated Setup (Recommended)
```bash
# Clone the repository
git clone https://github.com/yourusername/home-server-management.git
cd home-server-management

# Make scripts executable
chmod +x setup/*.sh scripts/**/*.sh

# Run the master setup script
sudo ./setup/master-setup.sh
```

### Option 2: Manual Step-by-Step Setup
Follow the detailed instructions in each section below, or run individual setup scripts:

```bash
# 1. Initial system setup
sudo ./setup/01-initial-setup.sh

# 2. Configure SSH
sudo ./setup/02-ssh-setup.sh

# 3. Set static IP
sudo ./setup/03-static-ip-setup.sh

# And so on...
```

### Option 3: Service-Specific Installation
Install only the services you need:

```bash
# Just Jellyfin media server
sudo ./setup/06-jellyfin-setup.sh

# Just Samba file sharing
sudo ./setup/05-samba-setup.sh

# Just VPN server
sudo ./setup/07-vpn-setup.sh
```

## 🚀 Initial Ubuntu Server Setup

### 1. Ubuntu Server Installation

1. Download Ubuntu Server Minimized ISO from [Ubuntu's official site](https://ubuntu.com/download/server)
2. Create bootable USB using tools like Rufus or Balena Etcher
3. Install Ubuntu Server on your spare laptop/server machine
4. During installation:
   - Set up user account and password
   - Enable SSH server when prompted
   - Configure network settings (can be changed later)

### 2. Initial Server Configuration

After installation, log into your server and update the system:

```bash
sudo apt update && sudo apt upgrade -y
```

Find your server details:
```bash
# Get username
whoami

# Get IP address
hostname -I
# or
ip addr show
```

## 🔐 SSH Configuration

### On Main Machine

Install SSH client (if not already installed):

**Ubuntu/Debian:**
```bash
sudo apt install openssh-client
```

**Windows:**
```bash
# SSH is built into Windows 10/11
# Or install via PowerShell:
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
```

**macOS:**
```bash
# SSH is pre-installed
```

### Connect to Server

```bash
ssh username@server_ip_address
```

Example:
```bash
ssh john@192.168.1.100
```

### SSH Key Authentication (Recommended)

Generate SSH key on main machine:
```bash
ssh-keygen -t rsa -b 4096 -C "your_email@domain.com"
```

Copy public key to server:
```bash
ssh-copy-id username@server_ip
```

## 🌐 Static IP Configuration

### Method 1: Netplan (Ubuntu 18.04+)

Edit netplan configuration:
```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

Example configuration:
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:  # Replace with your interface name
      dhcp4: no
      addresses:
        - 192.168.1.100/24  # Your desired static IP
      gateway4: 192.168.1.1  # Your router IP
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
```

Apply configuration:
```bash
sudo netplan apply
```

### Method 2: Router DHCP Reservation

1. Access your router's admin panel
2. Find DHCP settings
3. Add MAC address reservation for your server
4. Assign desired IP address

## ⚡ Wake-on-LAN Setup

### Enable WOL on Server

Install ethtool:
```bash
sudo apt install ethtool
```

Check if WOL is supported:
```bash
sudo ethtool interface_name | grep Wake-on
```

Enable WOL:
```bash
sudo ethtool -s interface_name wol g
```

Make permanent by adding to `/etc/rc.local`:
```bash
sudo nano /etc/rc.local
```

Add before `exit 0`:
```bash
ethtool -s interface_name wol g
```

### Wake Server from Main Machine

Install wakeonlan:
```bash
sudo apt install wakeonlan  # Linux
# or
brew install wakeonlan      # macOS
```

Wake the server:
```bash
wakeonlan MAC_ADDRESS
```

## 📁 Samba File Sharing

### Installation

```bash
sudo apt install samba samba-common-bin
```

### Configuration

Create shared directory:
```bash
sudo mkdir /srv/samba/shared
sudo chown nobody:nogroup /srv/samba/shared
sudo chmod 777 /srv/samba/shared
```

Configure Samba:
```bash
sudo nano /etc/samba/smb.conf
```

Add at the end:
```ini
[shared]
    path = /srv/samba/shared
    browseable = yes
    read only = no
    guest ok = yes
    create mask = 0755
```

Restart Samba:
```bash
sudo systemctl restart smbd
sudo systemctl enable smbd
```

Create Samba user:
```bash
sudo smbpasswd -a username
```

## 🎬 Jellyfin Media Server

### Installation

Add Jellyfin repository:
```bash
curl -fsSL https://repo.jellyfin.org/ubuntu/jellyfin_team.gpg.key | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/jellyfin.gpg
echo "deb [arch=$( dpkg --print-architecture )] https://repo.jellyfin.org/ubuntu $( lsb_release -c -s ) main" | sudo tee /etc/apt/sources.list.d/jellyfin.list
```

Install Jellyfin:
```bash
sudo apt update
sudo apt install jellyfin
```

### Access and Configuration

1. Open web browser: `http://server_ip:8096`
2. Follow setup wizard
3. Add media libraries pointing to your content directories
4. Configure users and permissions

### Media Directory Setup

Create media directories:
```bash
sudo mkdir -p /srv/media/{movies,tv,music}
sudo chown -R jellyfin:jellyfin /srv/media
```

## 🔒 VPN Server (OpenVPN)

### Installation

```bash
sudo apt install openvpn easy-rsa
```

### Quick Setup with Script

Download and run OpenVPN install script:
```bash
wget https://git.io/vpn -O openvpn-install.sh
sudo bash openvpn-install.sh
```

Follow prompts to configure:
- Server IP address
- Port (default 1194)
- DNS servers
- Client name

### Manual Configuration

For detailed manual setup, refer to the `setup/vpn-setup.sh` script in this repository.

## 🛡️ Security Considerations

### Firewall Configuration

Enable and configure UFW:
```bash
sudo ufw enable
sudo ufw default deny incoming
sudo ufw default allow outgoing

# SSH
sudo ufw allow 22/tcp

# Samba
sudo ufw allow 445/tcp
sudo ufw allow 139/tcp

# Jellyfin
sudo ufw allow 8096/tcp

# OpenVPN
sudo ufw allow 1194/udp
```

### SSH Hardening

Edit SSH configuration:
```bash
sudo nano /etc/ssh/sshd_config
```

Recommended changes:
```
Port 2222                    # Change default port
PermitRootLogin no          # Disable root login
PasswordAuthentication no   # Use keys only
MaxAuthTries 3             # Limit auth attempts
```

Restart SSH:
```bash
sudo systemctl restart sshd
```

### Regular Updates

Set up automatic security updates:
```bash
sudo apt install unattended-upgrades
sudo dpkg-reconfigure unattended-upgrades
```

## 🔧 Advanced Configuration

### System Performance Tuning

Optimize your server for better performance:

```bash
# Increase file descriptor limits
echo "* soft nofile 65536" | sudo tee -a /etc/security/limits.conf
echo "* hard nofile 65536" | sudo tee -a /etc/security/limits.conf

# Optimize network settings
echo "net.core.rmem_max = 134217728" | sudo tee -a /etc/sysctl.conf
echo "net.core.wmem_max = 134217728" | sudo tee -a /etc/sysctl.conf
echo "net.ipv4.tcp_rmem = 4096 65536 134217728" | sudo tee -a /etc/sysctl.conf
echo "net.ipv4.tcp_wmem = 4096 65536 134217728" | sudo tee -a /etc/sysctl.conf

# Apply changes
sudo sysctl -p
```

### Docker Alternative Setup

For users preferring containerized services:

```bash
# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Use docker-compose files in examples/docker-compose/
```

### Automated Backups

Set up automated configuration backups:

```bash
# Run the backup script
./scripts/maintenance/backup-configs.sh

# Add to crontab for daily backups
(crontab -l 2>/dev/null; echo "0 2 * * * /path/to/scripts/maintenance/backup-configs.sh") | crontab -
```

### Monitoring and Alerting

Monitor your server health:

```bash
# Check system health
./scripts/monitoring/health-check.sh

# Monitor disk usage
./scripts/monitoring/disk-usage-alert.sh

# Check all services
./scripts/monitoring/service-status.sh
```

## 🔧 Troubleshooting & Maintenance

### Common Issues and Solutions

**SSH Connection Refused:**
```bash
# Check SSH service status
sudo systemctl status ssh
sudo systemctl restart ssh

# Verify firewall settings
sudo ufw status
sudo ufw allow 22/tcp

# Check if SSH is listening
sudo netstat -tlnp | grep :22
```

**Samba Not Accessible:**
```bash
# Check Samba service
sudo systemctl status smbd nmbd
sudo systemctl restart smbd nmbd

# Test Samba configuration
sudo testparm

# Verify firewall ports
sudo ufw allow 445/tcp
sudo ufw allow 139/tcp

# Test connectivity
smbclient -L //server_ip -U username
```

**Jellyfin Web Interface Not Loading:**
```bash
# Check Jellyfin service
sudo systemctl status jellyfin
sudo systemctl restart jellyfin

# Check logs
sudo journalctl -u jellyfin -f

# Verify port accessibility
sudo netstat -tlnp | grep :8096
sudo ufw allow 8096/tcp
```

**Wake-on-LAN Not Working:**
```bash
# Check WOL support
sudo ethtool interface_name | grep Wake-on

# Enable WOL
sudo ethtool -s interface_name wol g

# Check BIOS settings
# Ensure "Wake on LAN" or "Power on by PCI-E" is enabled

# Test from another machine
wakeonlan MAC_ADDRESS
```

**VPN Connection Issues:**
```bash
# Check OpenVPN service
sudo systemctl status openvpn@server

# Check logs
sudo journalctl -u openvpn@server -f

# Verify firewall
sudo ufw allow 1194/udp

# Test port accessibility
sudo netstat -ulnp | grep :1194
```

### Regular Maintenance Tasks

**Weekly Tasks:**
```bash
# System updates
sudo apt update && sudo apt upgrade -y

# Check disk usage
df -h
du -sh /var/log/*

# Review service logs
sudo journalctl --since "1 week ago" | grep ERROR
```

**Monthly Tasks:**
```bash
# Full system cleanup
sudo apt autoremove -y
sudo apt autoclean

# Backup configurations
./scripts/maintenance/backup-configs.sh

# Security audit
./tests/security-audit.sh
```

### Performance Monitoring

**System Resource Usage:**
```bash
# CPU and memory usage
htop
# or
top

# Disk I/O
iotop

# Network usage
iftop
# or
nethogs
```

**Service-Specific Monitoring:**
```bash
# Jellyfin performance
curl -s http://localhost:8096/System/Info | jq

# Samba connections
sudo smbstatus

# VPN client status
sudo cat /var/log/openvpn/status.log
```

## 📝 Repository Structure

```
home-server-management/
├── README.md                           # This comprehensive guide
├── LICENSE                            # MIT License
├── setup/                            # Automated setup scripts
│   ├── 01-initial-setup.sh           # System updates and basic config
│   ├── 02-ssh-setup.sh               # SSH server hardening
│   ├── 03-static-ip-setup.sh         # Network configuration
│   ├── 04-firewall-setup.sh          # UFW firewall rules
│   ├── 05-samba-setup.sh             # File sharing server
│   ├── 06-jellyfin-setup.sh          # Media server installation
│   ├── 07-vpn-setup.sh               # OpenVPN server setup
│   ├── 08-wol-setup.sh               # Wake-on-LAN configuration
│   └── 99-security-hardening.sh      # Additional security measures
├── configs/                          # Configuration file templates
│   ├── netplan/
│   │   └── 00-installer-config.yaml  # Static IP configuration
│   ├── samba/
│   │   └── smb.conf.example          # Samba server config
│   ├── ssh/
│   │   └── sshd_config.hardened      # Secure SSH configuration
│   ├── openvpn/
│   │   └── server.conf.template      # VPN server template
│   └── systemd/
│       ├── wol-enable.service        # WOL service unit
│       └── server-monitor.service    # Health monitoring service
├── scripts/                          # Maintenance and utility scripts
│   ├── maintenance/
│   │   ├── backup-configs.sh         # Backup system configurations
│   │   ├── update-system.sh          # Automated system updates
│   │   └── cleanup-logs.sh           # Log rotation and cleanup
│   ├── monitoring/
│   │   ├── health-check.sh           # System health monitoring
│   │   ├── service-status.sh         # Check all services status
│   │   └── disk-usage-alert.sh       # Storage monitoring
│   └── utilities/
│       ├── wake-server.sh            # WOL script for main machine
│       ├── create-samba-user.sh      # Add new Samba users
│       └── jellyfin-backup.sh        # Backup Jellyfin config/database
├── docs/                             # Additional documentation
│   ├── TROUBLESHOOTING.md            # Common issues and solutions
│   ├── SECURITY.md                   # Security best practices
│   ├── NETWORKING.md                 # Network configuration details
│   └── SERVICES.md                   # Service-specific documentation
├── examples/                         # Example configurations and use cases
│   ├── docker-compose/               # Docker alternative setups
│   ├── advanced-configs/             # Advanced configuration examples
│   └── automation/                   # Automation workflow examples
└── tests/                           # Testing scripts and validation
    ├── connectivity-test.sh          # Network connectivity tests
    ├── service-validation.sh         # Validate service configurations
    └── security-audit.sh             # Basic security audit script
```

## 🤝 Contributing

We welcome contributions to improve this home server management guide! Here's how you can help:

### 🔧 **Types of Contributions**
- **Bug Fixes**: Fix issues in scripts or documentation
- **Feature Additions**: Add new services or automation scripts  
- **Documentation**: Improve guides, add tutorials, fix typos
- **Testing**: Test on different hardware/software configurations
- **Security**: Identify and fix security vulnerabilities

### 📋 **Contribution Process**

1. **Fork the Repository**
   ```bash
   # Click the "Fork" button on GitHub, then:
   git clone https://github.com/yourusername/home-server-management.git
   cd home-server-management
   ```

2. **Create a Feature Branch**
   ```bash
   git checkout -b feature/your-feature-name
   # or
   git checkout -b fix/issue-description
   ```

3. **Make Your Changes**
   - Follow existing code style and conventions
   - Test your changes thoroughly
   - Update documentation as needed
   - Add comments to complex code sections

4. **Test Your Changes**
   ```bash
   # Run validation scripts
   ./tests/connectivity-test.sh
   ./tests/service-validation.sh
   
   # Test on a fresh Ubuntu Server installation if possible
   ```

5. **Commit Your Changes**
   ```bash
   git add .
   git commit -m "feat: add monitoring script for system health"
   # Use conventional commits: feat:, fix:, docs:, test:, etc.
   ```

6. **Push and Create Pull Request**
   ```bash
   git push origin feature/your-feature-name
   # Then create a PR on GitHub
   ```

### 📝 **Coding Standards**

**Shell Scripts:**
- Use `#!/bin/bash` shebang
- Include error handling with `set -e`
- Add comments for complex operations
- Use descriptive variable names
- Test scripts on Ubuntu Server 20.04+ and 22.04+

**Documentation:**
- Use clear, concise language
- Include code examples
- Add troubleshooting sections
- Keep formatting consistent

**Configuration Files:**
- Include inline comments explaining settings
- Provide safe default values
- Document any security implications

### 🐛 **Reporting Issues**

When reporting bugs or issues:

1. **Use Issue Templates** (if available)
2. **Include System Information:**
   ```bash
   lsb_release -a
   uname -a
   systemctl --version
   ```
3. **Provide Logs:**
   ```bash
   sudo journalctl -u service-name --since "1 hour ago"
   ```
4. **Describe Expected vs Actual Behavior**
5. **Include Steps to Reproduce**

### 🌟 **Feature Requests**

For new features:
- Explain the use case and benefits
- Consider security implications
- Discuss implementation approach
- Check if it fits the project scope (home server management)

### 📋 **Development Setup**

For contributors wanting to test changes:

```bash
# Set up a virtual machine for testing
# Recommended: VirtualBox with Ubuntu Server

# Install development tools
sudo apt install shellcheck yamllint

# Validate shell scripts
shellcheck setup/*.sh scripts/**/*.sh

# Validate YAML files
yamllint configs/**/*.yaml
```

### 🏆 **Recognition**

Contributors will be recognized in:
- README.md contributors section
- CHANGELOG.md for significant contributions
- GitHub contributors page

Thank you for helping make this project better! 🚀

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## ⚠️ Disclaimer

This setup is intended for home/educational use. For production environments, additional security measures and professional consultation are recommended.

---

**Happy Server Management!** 🚀

For questions or issues, please open a GitHub issue or contribute to the project.
