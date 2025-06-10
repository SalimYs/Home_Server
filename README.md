# Home Server Management Guide

A comprehensive guide for setting up and managing a home server with Ubuntu Server, including remote access, media streaming, file sharing, and network services.

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

- **Server Machine**: Spare laptop/desktop with network capability
- **Main Machine**: Primary computer for administration
- **Network**: Router with DHCP capability
- **Storage**: Additional drives for media/file storage (optional)

## 🚀 Initial Setup

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

## 🔧 Troubleshooting

### Common Issues

**SSH Connection Refused:**
- Check if SSH service is running: `sudo systemctl status ssh`
- Verify firewall settings: `sudo ufw status`
- Confirm IP address: `ip addr show`

**Samba Not Accessible:**
- Check service status: `sudo systemctl status smbd`
- Verify firewall ports are open
- Test with: `smbclient -L //server_ip`

**Jellyfin Web Interface Not Loading:**
- Check service: `sudo systemctl status jellyfin`
- Verify port 8096 is open
- Check logs: `sudo journalctl -u jellyfin`

**Wake-on-LAN Not Working:**
- Verify WOL is enabled: `sudo ethtool interface_name | grep Wake-on`
- Check BIOS/UEFI settings for WOL support
- Ensure network cable is connected

## 📝 File Structure

```
home-server-management/
├── README.md
├── setup/
│   ├── initial-setup.sh
│   ├── ssh-setup.sh
│   ├── static-ip-setup.sh
│   ├── samba-setup.sh
│   ├── jellyfin-setup.sh
│   ├── vpn-setup.sh
│   └── wol-setup.sh
├── configs/
│   ├── smb.conf.example
│   ├── netplan-config.yaml.example
│   └── sshd_config.example
└── scripts/
    ├── backup.sh
    ├── update-system.sh
    └── monitor-services.sh
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## ⚠️ Disclaimer

This setup is intended for home/educational use. For production environments, additional security measures and professional consultation are recommended.

---

**Happy Server Management!** 🚀

For questions or issues, please open a GitHub issue or contribute to the project.
